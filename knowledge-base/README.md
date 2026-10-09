# Knowledge base

What we know, in a form someone else can reuse.

| Folder | Contents |
|---|---|
| [`patterns/`](patterns/) | Named patterns we have built at least once and written up. One file per pattern. |
| [`platform-matrix/`](platform-matrix/) | GCP vs AWS vs Azure, one row per capability, written only by someone who built it on that platform. |
| [`templates/`](templates/) | The documents every build produces: context pack, agent task contract, agent boundary doc, eval suite, readiness report. |
| [`glossary.md`](glossary.md) | Terms we use, in plain language. |

Every entry carries a header:

```
maturity: prototype | defined | in daily use
review: YYYY-MM-DD
source: <assignment id or session>
```

Entries past their review date are reviewed or retired. We don't accumulate.
