# Glossary

Plain-language definitions of terms used across this repo. Add a term the first time you have to explain it to someone.

| Term | Meaning |
|---|---|
| **Agentic system** | Everything that ships around an agent — context, tools, orchestration, state, trust, operations — not just the model and prompt. |
| **Plane** | One of the seven parts every agentic system has. See [reference model](../reference-model/). |
| **Action risk tier (T0–T3)** | How reversible and how consequential an action is. T0 read, T1 reversible write, T2 commit with sign-off, T3 irreversible. |
| **Run** | One execution of the system from request to terminal state. |
| **Trace** | The record of a run: every reasoning step, tool call, result and the terminal state. The source of truth for evals. |
| **Trajectory** | The sequence of tool calls in a run. Trajectory evals judge the path, not just the final answer. |
| **Reference dataset** | A small table of inputs, what the system did, and what it should have done. The starting point for any eval. |
| **Error analysis** | Reading the reference dataset to find one repeated failure pattern, not a list of mistakes. |
| **Rubric** | The criteria a domain expert would use to judge a decision, written down. |
| **LLM judge** | A model given a rubric and asked to score another system's output. One judge measures one thing. |
| **Judge calibration** | Testing the judge on hand-written cases until it agrees with a human. The judge is a system too. |
| **pass^k** | The share of tasks a system succeeds on *every* time across k attempts. The gap between pass^1 and pass^k is how unreliable it is. |
| **Confirm state** | A point in a run where the system must obtain explicit confirmation before an action fires. Modelled as a state, not a prompt. |
| **Hand-off** | The system deciding it cannot resolve the request within policy and stopping. A legitimate outcome, not a failure. |
| **Platform-native** | Built on the cloud platform's own agent SDK (ADK, AgentCore, Foundry Agent Service), not on a third-party framework. |
| **Harvest** | The step that closes an assignment: turning what was learned into a knowledge-base entry, pattern, matrix row or ruling. |
| **Ruling** | A decision the practice has made and stamped. It isn't reopened without a superseding ruling. |
