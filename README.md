# heuristics

Reusable mental models for Claude — invoke when the situation calls for it, ignore when it doesn't.

Not always-on system prompts. Not documentation you have to remember. Skills you reach for when a pattern applies.

## Before → After

**Before:** "I'll just dump everything in one folder and figure it out later." Three hours later, raw scraped HTML is mixed with your half-finished analysis and you can't tell what's source and what's inference.

**After:** Raw is always raw. You know exactly what stage each file is in. You can rerun any stage without touching the others.

## Skills

| Skill | Invoke | When |
|---|---|---|
| ETL research pipeline | `/heuristics:etl` | Starting any research, data gathering, or knowledge base build |

## Install

```
/plugin marketplace add codyhxyz/heuristics
/plugin install heuristics@heuristics
```

## Usage

Skills fire only on explicit invocation — nothing runs automatically.

```
/heuristics:etl
```

Drops the ETL pipeline heuristic into context: how to stage raw → validated → processed → interpreted → kb, which stages are optional, when to add them, and what never to skip.

## Example

```
user: I'm about to research competitors for a market analysis. How should I set this up?
claude: /heuristics:etl
→ [ETL pipeline heuristic loads]
→ Recommends research/raw/ to start, adds validated/ if sources vary in trust,
   interpreted/ for cross-source synthesis, kb/ if this needs to outlive the session.
```

## Why this exists

Pattern libraries rot in docs. Docs don't know when to show up. Skills do.

Every heuristic here is something that comes up repeatedly with Claude — patterns where the right structure prevents a class of mistakes. `/etl` exists because "dump everything flat and sort it later" is how research sessions fall apart.

More heuristics added as patterns mature.

## Adding a heuristic

See `CLAUDE.md` for contribution rules. Each heuristic = one skill file. On-demand only.
