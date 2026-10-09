# Platform comparison matrix

One row per capability. GCP (ADK / Agent Engine), AWS (Bedrock AgentCore), Azure (Foundry Agent Service).

**The rule:** a row is written only by someone who built that capability on that platform. Findings written before the build are vendor summaries; findings written after are evidence. Each cell carries ✅ / 🔍 / ⚠.

| Capability | GCP | AWS | Azure | Source | Review |
|---|---|---|---|---|---|
| Native agent-eval tooling — judge, trajectory metrics, trace format, gaps | 🔍 pending E-01 | 🔍 pending E-01 | 🔍 pending E-01 | E-01 | 2026-11-30 |

Rows for entry & control, the read loop, durable state, credentials and gates are added as those assignments close.
