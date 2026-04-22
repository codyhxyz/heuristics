---
name: etl
description: Use before starting any research, data gathering, or knowledge-base build to set up the right folder structure.
disable-model-invocation: true
---

# ETL Research Pipeline Heuristic

This borrows the ETL label from data engineering but maps it to research: raw source material (Extract), structured and normalized data (Transform), and a permanent knowledge base (Load). The stages in between — `validated/` and `interpreted/` — have no standard ETL equivalent; they exist because research involves trust-checking and synthesis that pure data pipelines skip.

Structure every research operation as discrete, non-overlapping stages. Raw data must never be overwritten or mixed with processed output.

## The rule

**Raw is always required. Every other stage is optional — add only what the scale demands.**

```
research/
├── raw/          ← ALWAYS. Original source material, unmodified.
├── validated/    ← Optional. Checked for accuracy, deduped, confirmed legit.
├── processed/    ← Optional. Parsed, normalized, structured.
├── interpreted/  ← Optional. Analysis, synthesis, conclusions drawn.
└── kb/           ← Optional. Final knowledge base — what survives distillation.
```

## Stage definitions

| Stage | What goes here | When to add it |
|---|---|---|
| `raw/` | Unmodified source: scraped pages, API dumps, downloaded files, copied text. e.g. `raw/2026-04-21-techcrunch-article.html` | Always. Never skip. |
| `validated/` | Sources confirmed accurate, deduped, relevance-filtered | When raw volume is high or trust varies across sources |
| `processed/` | Parsed, normalized, structured (JSON, tables, extracted fields) | When raw is unstructured and downstream needs clean data |
| `interpreted/` | Synthesis, patterns, conclusions, open questions | When you need to reason across multiple sources |
| `kb/` | Distilled permanent reference — survives the project, gets queried later | When the research output needs to outlive the current task |

## Non-negotiables

1. **Never overwrite raw.** Raw is append-only. If a source changes, add a new timestamped file — don't edit the original.
2. **Never skip raw by writing directly to a later stage.** Even if you think you won't need it, raw is insurance.
3. **One direction only.** Data flows raw → validated → processed → interpreted → kb. Never backward — if analysis pollutes raw, you can no longer distinguish what the source said from what you inferred.
4. **Stage boundaries are hard.** A file belongs in exactly one stage. If you're unsure, put it one stage earlier.

## Minimal setup (most operations)

Most tasks only need:
```
research/
└── raw/
```
That's it. Add stages only when the current stage becomes a bottleneck or the scope grows.

## Scale signals

| Signal | Add this stage |
|---|---|
| Sources vary in quality / trust | `validated/` |
| Raw is unstructured (HTML, PDFs, freeform text) | `processed/` |
| Multiple sources need cross-referencing | `interpreted/` |
| Output needs to be reused in future sessions | `kb/` |

## Common mistakes

- Putting analysis in `raw/` — raw is source-only, no commentary
- Skipping `raw/` because "I'll remember where it came from" — you won't
- Merging `processed/` and `interpreted/` — parsing ≠ reasoning
- Building `kb/` before you have `interpreted/` — distillation requires synthesis first
