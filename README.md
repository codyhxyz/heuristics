# heuristics

A grab-bag of heuristics for using Claude Code. Think of this as my library of prompts.
Model invocation turned off to save you context. These are for humans to use as-needed, not for AI to worry about.

## Skills

| Skill | Invoke | What it does |
|---|---|---|
| ETL research pipeline | `/heuristics:etl` | Teaches Claude to stage raw data into /raw before processing, and other ETL pipeline grammar skills. |
| Tournament | `/heuristics:tournament` | Dispatches N parallel drafters against one task, ranks them with a decisive judge, and optionally synthesizes a best-of-all output. For subjective deliverables where diverse angles strengthen the result. |

## Install

```
/plugin marketplace add codyhxyz/heuristics
/plugin install heuristics@heuristics
```
