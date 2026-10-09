# The reference model

Every agentic system, however small, has seven parts. Simple systems have thin versions of each; complex systems have thick ones. None are optional.

| # | Plane | What it covers | One question it answers |
|---|---|---|---|
| 1 | **Cognition** | The model, its instructions, the reasoning loop, the harness around it | What decides what to do next? |
| 2 | **Context** | Retrieval, knowledge bases, memory, what the model is shown and what it is kept from | What does it know when it decides? |
| 3 | **Action** | Tools, MCP servers, connectors — each with its own business logic | What can it actually do? |
| 4 | **Orchestration** | Topology, agent-to-agent, sync/async, sagas, event mesh | How do runs and agents coordinate? |
| 5 | **State & Data** | Run state, persistence, replay, idempotency | What happened, and can we prove it? |
| 6 | **Trust** | Identity for humans *and* agents, scoped credentials, guardrails, human sign-off, action risk tiers | What is it allowed to do, and who said so? |
| 7 | **Operations** | Evals, traces, cost and latency, deployment, incident recovery | Is it working, and how would we know? |

**The numbers are anatomy, not build order.** We build in dependency order: State & Data first (everything else writes to it), then Trust, then Operations, then Action, Context, Orchestration — and Cognition last, because the model is the most replaceable part and is chosen once everything around it is settled.

**Action risk tiers** (used throughout the Trust plane and in every assignment):

| Tier | Meaning | Example |
|---|---|---|
| T0 | Read only | Look up an order |
| T1 | Reversible write | Add a note; cancel an order that can be reinstated |
| T2 | Commit — needs sign-off | Issue a refund |
| T3 | Consequential — irreversible, high impact | Delete a customer account |

Team builds stay at T0–T1. T2 and above are designed and modelled, never executed in a lab build.

Full plane-by-plane detail and the runtime blueprint are published here as each design stage lands.
