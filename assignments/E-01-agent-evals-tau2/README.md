# Build Assignment E-01 — Agent Evals on tau2-bench

**AI Engineering Practice · Giant Leap Systems**
**Status:** `v0.1` — issued 09 October 2026 · **Duration:** 2 weeks (Mon 13 Oct → Fri 24 Oct 2026)
**Teams:** GCP (ADK + Agent Engine) · AWS (Bedrock AgentCore) · Azure (Foundry Agent Service) — same build, three platforms
**Track:** Evals (E-series). Independent of the Slice 01–07 build line.

---

## 1. The business problem

**NorthCart** is a mid-size online retailer (synthetic client; the domain, tools and policies come from the public tau2-bench retail environment). Its support desk handles order status, cancellations, modifications, exchanges and returns. Every one of those actions is governed by a written policy: what can be cancelled and when, which items are exchangeable, what a customer must confirm before an order is changed.

NorthCart has already seen a vendor demo of an AI support agent. It looked good. The CTO's question was not "can it do it" — it was:

> "Before I let this thing touch a customer's order, how do I know it follows our policy? And when you change the prompt or the model next month, how do I know it still does?"

That is the problem. The agent is not the hard part. **The evidence is.** NorthCart will not ship an agent that can modify orders without a repeatable, numeric answer to: *did it do the right thing, did it do it the right way, and does it keep doing it.*

What a wrong answer costs them: a cancellation the policy did not allow, an exchange for an item that was not exchangeable, a customer told their order shipped when it did not. These are not stack traces. They are confident, polite, wrong actions — and nobody finds out until the customer complains.

## 2. What we are building

Two things, and the second is the deliverable:

1. **A support agent for NorthCart's retail desk** — a single agent that reasons, calls a tool, reads the result, and decides again, until the customer's request is resolved or it hands off. It runs against the tau2-bench retail environment (its tools, its policy document, its simulated customers). It can read anything and can perform the reversible order actions the policy allows. Anything the policy marks as needing explicit customer confirmation is modelled as a stop-and-confirm state.

2. **An eval harness that answers the CTO's question** — a reference dataset, one calibrated LLM judge for the decision that matters most, three code-based checks computed from the agent's trace, and a repeatability score (pass^k). Run end to end, it produces a one-page readiness report per platform, and the same report can be re-run after any change.

Each team builds both on its own platform. At the end, the three readiness reports sit side by side.

**Explicitly not in scope:** multi-agent (planner/executor) topology; airline and telecom domains; building a real human-approval workflow (model it as a state, do not build the inbox); production deployment; online/continuous evals.

## 3. Why

**Why this project.** Every build so far has ended with a demo. A demo tells you the system worked once, in front of you. NorthCart's CTO is asking the question every client CTO will ask, and we do not yet have a practised answer. After this assignment, every engineer on the team will have built the answer once, by hand, on their platform.

**Why tau2-bench.** It is a public benchmark with a real policy document, real tool definitions, a simulated customer that pushes back, and ground truth for each task. That gives all three teams the *same* tasks and the *same* correct answers, so the three readiness reports are comparable. We did not have to invent any of it. ✅

**Why one judge, not ten.** The method we are following (reference dataset → error analysis → name one failure → rubric → judge) insists on one failure pattern per eval. That is the discipline. A judge that measures "quality" measures nothing; a judge that measures "did the agent correctly stop and confirm before changing an order" measures one thing and can be calibrated.

**Why pass^k.** A single successful run tells you almost nothing about a probabilistic system. The same task can succeed once and fail the next three times. pass^k asks whether the agent succeeds *every* time across k attempts. That is the difference between a demo and an evaluation, and it is the number the CTO actually wants.

**Why platform-native.** We are building this on ADK/Agent Engine, AgentCore and Foundry Agent Service directly — not on LangGraph, even though the source material uses it (standing ruling I-6). Each platform now ships its own agent-evaluation tooling. We use that first and write code only for what it does not give us. The list of what we had to write ourselves is one of the most valuable things this project produces.

## 4. How

### 4.1 The two weeks

| Week | Focus | Exit |
|---|---|---|
| **1 — Build** (13–17 Oct) | Agent running against tau2 retail; traces exported in the common schema; 10 tasks selected; reference dataset started | Friday demo: 3 tasks end to end, one trace file per run, dataset with ≥20 rows |
| **2 — Evaluate** (20–24 Oct) | Error analysis → named eval → rubric → judge → calibrate; code checks; pass^k; readiness report | Friday readout: three readiness reports side by side, gap list per platform |

Checkpoints: **Wed 15 Oct** (agent loop working on at least one task, trace schema agreed across teams), **Wed 22 Oct** (judge calibrated, code checks running).

### 4.2 Week 1 — build the agent and the trace

**Day 1–2 · Environment and data.** Clone `sierra-research/tau2-bench`. Everything this project needs ships inside the repo — you create no data and fetch none from anywhere else. ✅

| What | Where in the repo | Used for |
|---|---|---|
| NorthCart's database — products, users, orders (~2.8 MB JSON) | `data/tau2/domains/retail/db.json` | The state every tool reads and writes. Reset it between runs (deep-copy the seeded file; tau2's own evaluator does the same). |
| The operational policy | `data/tau2/domains/retail/policy.md` | Agent instruction set, verbatim. Rubric source in Week 2. |
| 114 tasks, each with the simulated customer's instructions and the expected actions / expected end state | `data/tau2/domains/retail/tasks.json` | Task scenarios for the runs; ground truth for task success and tool-call accuracy. |
| Official task split | `data/tau2/domains/retail/split_tasks.json` | Pick your 10 from the base split so results are comparable to published numbers. |
| 16 retail tools | `src/tau2/domains/retail/tools.py` | Wrap these as your platform's native tool definitions. Do not rewrite their logic. |
| User simulator and evaluator | repo code (`tau2` package) | Run locally with your own model key. |

Stand up the retail domain locally and run tau2's own agent once on one task so you have seen a working baseline before you build yours. Read the policy document end to end — you will write the rubric from it in Week 2.

**Day 2–4 · Agent.** Build one agent on your platform's native SDK:

- The tau2 retail tools exposed as the agent's tools (platform-native tool definitions; MCP is acceptable if the platform supports it natively).
- The policy document as the agent's instruction set.
- A bounded loop: maximum turns and maximum tool calls per run are configuration, not hopes.
- Read actions execute directly. Reversible order actions (cancel, modify, exchange, return) execute against the simulated environment. Any action the policy says requires explicit customer confirmation goes through a **confirm state**: the agent must obtain confirmation from the (simulated) customer before the tool call fires. If confirmation is refused, the run ends cleanly with a recorded reason.
- Hand-off: when the agent cannot resolve within policy, it says so and stops. That is a legitimate outcome, not a failure.

**Day 3–5 · Trace export.** Every run writes one trace file in the **common trace schema** (§4.5), regardless of what the platform's own observability shows. This file is what the Week-2 evals read. If your platform emits OpenTelemetry/GenAI-semantic spans, convert them; do not hand-write.

**Day 4–5 · Task selection and reference dataset.** Pick **10 tasks** from tau2 retail (all three teams use the same 10 — agree the list by Wednesday). Mix: at least 3 read-only, at least 4 involving a reversible action, at least 2 where the correct outcome is "cannot do this under policy". Run each task once. Record, per run: `task_id`, `user_request` (the task scenario), `agent_outcome` (what it did), `reference_outcome` (what tau2 says it should have done). That is the reference dataset — minimum 20 rows by Friday (run tasks more than once; variance is data).

### 4.3 Week 2 — build the evidence

**Step 1 · Error analysis (Mon).** Sit together. Read the rows. You are looking for *one repeated failure pattern*, not a list of mistakes. Likely candidates in a retail support agent: acting without confirmation; refusing something the policy allows; approving something the policy forbids; calling the wrong tool with the right intent. Write down the pattern in one sentence.

**Step 2 · Name the eval (Mon).** One failure pattern, one eval. Expected: **confirmation-gate correctness** — *did the agent stop and confirm before every action the policy requires confirmation for, and not stop for actions that do not require it?* If your error analysis finds a different dominant pattern, name that instead and say why in the report.

**Step 3 · Write the rubric (Tue).** The criteria a NorthCart support lead would use to make the same call, written from the policy document. Short, specific, in plain language. The rubric is domain knowledge, not prompt engineering.

**Step 4 · Build the judge (Tue–Wed).** Use your platform's native evaluator for custom LLM-judge evals (Vertex Gen AI Evaluation custom metric · AgentCore custom evaluator · Foundry custom evaluator) 🔍 confirm current SDK shape. Rails: `correct` / `incorrect`. Inputs: the user request and the agent's trace. **The reference outcome never reaches the judge.** It judges the decision, not the match.

**Step 5 · Calibrate the judge (Wed).** The judge is a system too. Hand-write **5 adversarial cases** — a frustrated customer who has asked three times; a request that sounds like it needs confirmation but does not; a confirmation that was asked for but ignored. Run the judge. If it gets any wrong, the rubric is wrong, not the case. Fix and re-run until 5/5. Record what you changed.

**Step 6 · Code-based checks (Wed–Thu).** Three deterministic checks, computed from the trace file, no LLM:

| Check | Computed how | Native evaluator covers it? 🔍 |
|---|---|---|
| **Task success** | Terminal state of the simulated environment matches tau2 ground truth for the task | Partly — record what the platform gives you |
| **Tool-call accuracy** | Tool calls made vs. the reference trajectory for the task: right tools, right arguments, no redundant calls | Vertex trajectory metrics · AgentCore tool-usage evaluators · Foundry Tool Call Accuracy — use them, then note gaps |
| **Loop-bound compliance** | Turns and tool calls stayed within configured limits; run terminated with an explicit reason | Almost certainly not — write it |

Plus one **hand-off correctness** count: of the tasks whose correct outcome is "cannot do under policy", how many did the agent hand off rather than attempt.

**Step 7 · pass^k (Thu).** Run each of the 10 tasks **5 times**. pass^5 for a task = 1 only if all 5 runs succeed. Report pass^1 (average success) *and* pass^5 side by side — the gap between them is the headline.

**Step 8 · Readiness report (Fri).** One page per platform (template §4.6). Readout: three reports side by side, then the cross-platform question — *where do the numbers diverge, and is it the model, the SDK, or our code?*

### 4.4 Platform notes

| | GCP | AWS | Azure |
|---|---|---|---|
| Agent runtime | ADK, deployed to Agent Engine (the runtime under Gemini Enterprise) | AgentCore Runtime | Foundry Agent Service |
| Native eval tooling ✅ exists / 🔍 verify details | Gen AI Evaluation Service — final-response + trajectory metrics, custom metrics | AgentCore Evaluations (GA Mar 2026) — 13 built-in evaluators, custom LLM and Lambda evaluators, reads OTEL traces | Azure AI Evaluation SDK — Intent Resolution, Task Adherence, Tool Call Accuracy, Tool Input Accuracy, Tool Call Success (agent evaluators in preview) |
| Trace source | ADK events → common schema | AgentCore Observability / OTEL → common schema | Foundry thread + run steps → common schema |

The row that matters for the practice: **what did the native tooling give you for free, what did you have to write, and what could you not get at all.** Keep a running list from Day 1.

### 4.5 Common trace schema (all three teams, agreed by Wed 15 Oct)

One JSON file per run. Minimum fields:

```
run_id, task_id, platform, model, started_at, ended_at,
config: { max_turns, max_tool_calls },
steps: [ { step_no, type: reason|tool_call|tool_result|confirm_request|confirm_response|final|handoff,
           tool_name?, args?, result?, text?, ts } ],
terminal: { state: resolved|handed_off|confirm_refused|bound_exceeded|error, reason },
counts: { turns, tool_calls },
cost: { input_tokens, output_tokens }, latency_ms
```

The pass^k script and the code checks are written **once**, against this schema, and shared across teams. If a platform cannot populate a field, record `null` and say why — that is a finding.

### 4.6 Readiness report — one page per platform

1. System under test: platform, model, SDK versions, date
2. Reference dataset: rows, task mix
3. Named eval + rubric (verbatim) + calibration result (5/5, what changed)
4. Judge result over all runs: % correct, with the incorrect rows listed
5. Code checks: task success, tool-call accuracy, loop-bound compliance, hand-off correctness
6. pass^1 vs pass^5 per task, and overall
7. Cost and latency per run (median, P95)
8. Native-tooling gap list: used / wrote ourselves / could not do
9. Would we ship this to NorthCart? One paragraph, honest.

### 4.7 Done when

- [ ] Agent runs all 10 tasks end to end on the native SDK, with confirm state and hand-off working
- [ ] Every run produces a trace in the common schema; the shared scripts run against it unmodified
- [ ] Reference dataset ≥ 20 rows; error analysis written up; one named eval
- [ ] Judge built on the native evaluator, calibrated 5/5 on adversarial cases, with the rubric revisions recorded
- [ ] Three code checks + hand-off count computed from traces
- [ ] pass^1 and pass^5 reported for all 10 tasks
- [ ] Readiness report delivered; gap list complete
- [ ] Cross-platform readout held

### 4.8 Rules

- Platform-native SDK only. No LangGraph, no LangChain. ✅ (ruling I-6)
- Synthetic data only. All data is the tau2 retail fixture set (§4.2); nothing else enters, and `db.json` is reset to seed between runs.
- Reversible actions only, against the simulated environment. Nothing in this build touches a real system.
- The judge never sees the reference answer.
- Numbers come from the trace file, not from a dashboard screenshot.
- When the platform's eval tool and your own check disagree, record both. Do not pick the one that looks better.

### 4.9 What the practice harvests (not the team's concern during the build)

- **Eval suite pattern** (maturity: prototype) — the dataset → one eval → rubric → calibrated judge → code checks → pass^k sequence, as a reusable template
- **Platform comparison matrix row** — native agent-eval tooling: coverage, gaps, trace format
- **Ruling (proposed)** — an offline eval harness of this shape is a done-criterion for every build from the next slice onward; trajectory replay from real runs stays where it is in the slice map
- **Glossary entries** — reference dataset, error analysis, rubric, judge calibration, pass^k, trajectory, confirm state

## 5. Source material

- Project 5 and the per-project evals list: `aishwaryanr/awesome-generative-ai-guide · youtube/ai-engineer-projects.md` (Oct 2026)
- The five-step method and the QuickCart worked example: `youtube/ai-evals-starter-kit.md` and `youtube/ai-evals.md` (Sep 2026) — read the starter kit before Week 2 Monday
- tau2-bench: `sierra-research/tau2-bench` (Barres et al., 2025)
- Platform docs: Vertex AI *Evaluate Gen AI agents*; Amazon Bedrock AgentCore *Evaluations*; Microsoft Foundry *Agent evaluators* and *Evaluate agents with the SDK* 🔍 re-check on Day 1, these move monthly

---

## Drift log

| Date | Change | Reason |
|---|---|---|
| 2026-10-09 | v0.1 issued. E-series opened as a track independent of the slice build line. | Evals pulled forward rather than waiting for Slice 06; tau2 chosen over the invoice agent as system under test so all three platforms score against shared ground truth. Multi-agent explicitly excluded — it belongs to the build journey, not this track. |
