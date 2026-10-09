# Assignments

One folder per assignment. Each contains the spec (`README.md`, so it renders here), anything shared across the three teams (schemas, scripts), and links to the team build repos.

| ID | Title | Dates | Status | Readout |
|---|---|---|---|---|
| E-01 | [Agent Evals on tau2-bench](E-01-agent-evals-tau2/) | 13–24 Oct 2026 | In flight | — |

## Series

- **E-series** — evals track. Independent of the build line; builds a shared eval harness and the discipline of using it.
- **Build line (Slices 01–07)** — the system built one vertical slice at a time. Specs to be added here as each slice is issued.

## Folder layout

```
assignments/<id>/
  README.md        the spec
  shared/          trace schema, shared scripts, task lists — things all teams use unchanged
  gcp.md aws.md azure.md   each team's build notes and link to their repo (added by the team)
```
