# How we work

## Assignments

Every assignment follows the same loop: **brief → build → review → harvest.**

- **Brief.** A spec lands in `assignments/<id>/`. It opens with the business problem and says what, why and how. Read it before you open an IDE.
- **Build.** Each cloud team (GCP, AWS, Azure) builds the same thing on its platform's native SDK. Code lives in that team's build repo, linked from the assignment folder.
- **Review.** Checkpoints are in the spec. Friday demos are to the whole team, not to the lead.
- **Harvest.** An assignment is not closed when the demo works. It closes when it has produced at least one of: a knowledge-base entry, a named pattern, a platform-matrix row, or a ruling. The readout in `readouts/` records which.

## Rules that don't move

1. **Platform-native SDKs only** for team builds — ADK/Agent Engine, Bedrock AgentCore, Foundry Agent Service. No LangChain, no LangGraph. Findings on a framework are not findings on the platform.
2. **Synthetic data only.** No client data enters this repo or any team build.
3. **Reversible actions only.** Team builds never take an action that cannot be undone. Anything beyond that is designed, discussed, and modelled as a state — not built.
4. **Numbers come from traces, not screenshots.** If a result can't be recomputed from a trace file, it isn't a result.
5. **Evals are not deferred.** From E-01 onward, every build ships with a reference dataset and at least one calibrated eval.

## Getting something into the knowledge base

1. Use the matching template in `knowledge-base/templates/`.
2. Set the maturity tag honestly: `prototype` (built once), `defined` (built twice, written up), `in daily use` (teams reach for it without being told).
3. Set a review date. Entries past their review date get reviewed or retired.
4. Open a pull request. One entry per PR. The practice lead merges.

## Writing

- Plain language first. If a term needs explaining, add it to the glossary.
- Short. A pattern that needs three pages isn't a pattern yet.
- Tag claims: ✅ confirmed · 🔍 directional · ⚠ assumption.
- Vendor product names only in the platform matrix and in platform-specific build notes. Everywhere else, use the class: "workflow engine", "event mesh", "object store".

## Files

- `.md` is the authoritative form of every document. Renderings (`.docx`, `.html`, `.pdf`) may sit alongside but are never edited directly.
- Folder and file names: lowercase, hyphenated, no spaces.
