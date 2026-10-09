# AI Engineering — Giant Leap

The shared knowledge base for Giant Leap's AI engineering team: assignments, the reference model we design against, patterns we've proven, platform comparisons, templates, and a learning path.

**What this repo is for.** One place where everything the team learns lands in a form someone else can reuse. If you built something and the only record is a demo, it isn't here yet.

**What it is not.** Not a code repo. Each build lives in its own repository; this repo holds the spec that drove it, the findings that came out of it, and the readout. Link to code, don't copy it in.

## Where things are

| Folder | What's in it | Start here if… |
|---|---|---|
| [`assignments/`](assignments/) | Every assignment issued to the team — the spec, the shared scripts, the readout | You've just been given an assignment |
| [`reference-model/`](reference-model/) | The seven-plane model of an agentic system, in plain language | You want to know what "a complete system" means here |
| [`knowledge-base/`](knowledge-base/) | Patterns, platform comparison matrix, templates, glossary | You're designing something and want to know what we already know |
| [`learning/`](learning/) | Reading path and session notes | You're new, or you want to go deeper on a topic |
| [`readouts/`](readouts/) | What we found at the end of each assignment, across teams | You want the conclusions without the build |
| [`how-we-work.md`](how-we-work.md) | How assignments run, how entries get in, what "done" means | Before your first contribution |

## The one idea everything here rests on

The unit of engineering is the **agentic system**, not the agent. The model, the prompt and the reasoning loop are 10–20% of what ships. The rest — context, tools, orchestration, state, trust, operations — is where production systems succeed or fail. The [reference model](reference-model/) names those parts; the assignments build them one slice at a time.

## Current

- **In flight:** [E-01 — Agent Evals on tau2-bench](assignments/E-01-agent-evals-tau2/) · 13–24 Oct 2026 · all three cloud teams

## Conventions in brief

- Plain language. Internal shorthand and codes are explained in the [glossary](knowledge-base/glossary.md) or not used.
- Every knowledge-base entry carries a maturity tag (`prototype` / `defined` / `in daily use`) and a review date. Superseded entries are retired, not accumulated.
- Claims are tagged: ✅ confirmed · 🔍 directional, verify before relying on it · ⚠ assumption or risk.
- `.md` is authoritative. Anything else (docx, html, pdf) is a rendering.
