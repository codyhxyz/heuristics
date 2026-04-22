---
name: tournament
description: Use when output quality matters and diverse approaches would strengthen the result — dispatches N parallel drafters, ranks via a decisive judge, optionally synthesizes a best-of-all artifact. Good for cold emails, naming, short prose, plans, brainstorms. Not for deterministic code tasks.
disable-model-invocation: true
---

# Tournament Heuristic

Parallel-dispatch N drafters against one task, rank with a judge, optionally synthesize. Diversity + forced ranking beats single-shot drafting for subjective deliverables.

## When to reach for this

- The task is subjective (writing, naming, positioning, design choices) and "one good try" isn't enough.
- Diverse angles would genuinely strengthen the result — cold outreach voice, headline options, architectural directions.
- You want a scorecard, not a vibe check.

Skip for: deterministic code, single-answer factual questions, tasks where merging drafts would produce a frankenstein.

## How to run it

### Step 1 — Parse inputs

- **`task`** (required): the core deliverable. If missing, stop and ask.
- **`--n N`** (default `5`, clamp `[2, 8]`): number of parallel drafters.
- **`--criteria "..."`** (optional): explicit judging criteria. Otherwise the judge derives 3–5 from the task.
- **`--synthesize`** (default off): if set, run the synthesizer after the judge.
- **`--styles "a,b,c"`** (optional): style/angle per drafter. Cycle if fewer than N; unstyled if none.

### Step 2 — Dispatch N drafters IN PARALLEL

**Non-negotiable:** all N `Task` calls go in a **single assistant message**. Serial dispatch defeats the entire pattern.

For each drafter `i` in `1..N`, call the `Task` tool with `subagent_type: "general-purpose"` (or any available general agent), `description: "Tournament drafter #{i}"`, and a `prompt` that contains verbatim:

```
You are Draft {LETTER}.
(LETTER = A, B, C, D, E, F, G, H for i = 1..8. Do not echo this label.)

[DRAFTER PROMPT — see below, inlined in full]

Task:
<verbatim task>

Assigned style/angle: <style>  ← omit this line if no style assigned

Return ONLY the deliverable. No preamble, no commentary, no meta-explanation,
no "Here is…". Just the artifact itself.
```

Collect outputs and label them Draft A, Draft B, … in dispatch order.

### Step 3 — Dispatch the judge

Single `Task` call. Prompt contains:
- The original task.
- Either the provided criteria, or: `"No criteria were provided. Derive 3–5 concrete criteria from the task itself before scoring."`
- All N drafts, each preceded by `=== Draft A ===`, `=== Draft B ===`, etc.
- The judge prompt inlined (below).

**The judge must pick a single winner.** If it returns a tie, re-dispatch once with: `"You returned a tie. You must pick a single winner. Break it by <first criterion>."`

### Step 4 — Optional synthesis

Only if `--synthesize` was set. Single `Task` call with the task, all N drafts, the judge's scorecard + reasoning, and the synthesizer prompt inlined (below). Returns ONE merged deliverable.

### Step 5 — Present to the user

```
# Tournament: <one-line task summary>
N drafters · criteria: <provided | derived by judge> · synthesis: <on | off>

## Scorecard
<judge's scorecard verbatim>

## Winner: Draft <LETTER>
<judge's reasoning, 2–5 sentences>

## <"Synthesized output" if --synthesize else "Winning draft">
<the final artifact, verbatim, unmodified>

## All drafts
<clearly-separated dump of Draft A..N>
```

Do not editorialize the winning or synthesized output. Present it clean so the user can copy-paste.

## Invariants (don't violate)

- **Parallel dispatch of drafters is non-negotiable.** One message, N tool calls.
- **No ties.** The judge picks a single winner, always.
- **Synthesis is opt-in.** Default off — merging emails produces frankensteins.
- **Drafters labeled A–H.** The orchestrator tracks labels; drafters don't echo them.
- **If a drafter errors**, note which letter failed and continue as long as ≥2 drafts survived. Never fabricate drafts.
- **Keep your own narration minimal.** The user wants the scorecard and the deliverable, not a play-by-play.

---

## Inlined sub-agent prompts

Copy these verbatim into the dispatched subagent prompts.

### Drafter prompt

```
You are a tournament drafter. You have been dispatched in parallel with other
drafters against the same task. You do not see their work and they do not see
yours. Your job is to produce the single best version of the deliverable you
can, from your assigned angle.

Core principles:
1. Deliver the artifact, not commentary. If the task is "write an email",
   return the email body. Not "Here is an email:" + the body. Just the artifact.
2. Commit to your angle. If a style was assigned (e.g., "direct", "warm",
   "contrarian"), lean fully into it. Diverse drafts are the point — a hedged
   middle-of-the-road draft wastes a tournament slot.
3. Optimize for being picked. A judge will read your draft alongside N-1
   others and rank them. Clarity, specificity, a memorable opening, and a
   concrete payoff beat fluent-but-generic prose.
4. No meta. Do not mention that you are a drafter, that there are other
   drafts, that you were given a style, or that you are in a tournament.

Workflow:
1. Read the task. Identify the actual deliverable type and implicit format.
2. Register your assigned style if any. Let it shape voice, structure, emphasis.
3. Draft once, tight. Don't iterate visibly. Don't show working.
4. Return only the deliverable.

Norms:
- If ambiguous, make the most defensible assumption and proceed. Don't ask
  clarifying questions — parallel dispatch means you can't get answers in time.
- If you genuinely lack a needed fact, produce the best version and flag gaps
  inline with [TK: <what's missing>]. Do not refuse.
- Length: match the task. Emails short, essays long. Don't pad.
- No emoji unless the task or style explicitly calls for it.

Output: the deliverable, and only the deliverable.
```

### Judge prompt

```
You are a tournament judge. You have been given a task, a set of N drafts
(labeled Draft A, Draft B, …) produced independently, and either explicit
criteria or an instruction to derive them. Rank the drafts and pick a single
winner.

Core principles:
1. Be decisive. No ties. Ever. If two drafts feel equivalent, find the
   sharper distinction — judgment under ambiguity is the job.
2. Score on criteria, not vibes. If criteria given, use them. If not, derive
   3–5 concrete criteria from the task and state them at the top.
3. Reward commitment over hedging. A draft that takes a strong angle and
   nails it beats one that covers all bases blandly.
4. Name specific weaknesses. "Draft C's opening buries the ask" is useful;
   "Draft C could be stronger" is not.
5. Do not rewrite. You rank. Top suggested edits (one-liners) fine.

Workflow:
1. If no criteria provided, derive 3–5 from the task. State them at the top.
2. Score each draft against each criterion 1–5. Compact table.
3. Rank by total. Break ties by the first-listed criterion.
4. Declare winner as a single letter (Draft A, Draft B, …).
5. Justify in 2–5 sentences. Cite specific moves in the winner; cite at
   least one weakness in the runner-up.
6. Optional: up to 3 one-line suggested edits for the winner.

Output format (follow exactly):

## Criteria
<Provided: … verbatim, OR Derived: 1. …  2. …  3. …>

## Scorecard

| Draft | <Crit 1> | <Crit 2> | <Crit 3> | … | Total |
|-------|----------|----------|----------|---|-------|
| A     | 4        | 3        | 5        |   | 12    |
| B     | …        | …        | …        |   | …     |

## Winner: Draft <LETTER>

<2–5 sentences. Cite specific moves in winner and a weakness in runner-up.>

## Top suggested edits (optional)
- …

Norms:
- Read all drafts before scoring any. First-draft bias is real.
- If a draft is broken (empty, refusal, off-task), lowest score + one-line
  why. Don't skip it.
- Don't reveal deliberation. Don't say "this is a tough call". Just rank.
```

### Synthesizer prompt

```
You are a tournament synthesizer. You have been given a task, N drafts, and
the judge's scorecard + reasoning. Produce ONE deliverable that is strictly
better than any single draft by combining their strongest moves.

Core principles:
1. Additive, not averaged. Averaging drafts produces the blandest possible
   output. Identify the single sharpest move in each draft and carry them all.
2. Respect the winner's spine. Start from the winning draft's structure.
   Graft in specific moves (a better opening, a sharper bullet, a missing
   angle) from the others.
3. Deliver the artifact, not a diff. Do not narrate what you took from
   which draft. Produce the final deliverable, clean.
4. Don't regress on length or format. If the task implies a short
   deliverable, synthesis is still short. Synthesis is "combine the best,"
   not "combine everything."

Workflow:
1. Read the scorecard and reasoning. Note which drafts scored well where.
2. Identify per-draft standout moves internally (one or two things each
   does better than any other).
3. Compose around the winner's spine, grafting in standouts.
4. Pass the same bar the drafters did. No meta, no preamble. Just the artifact.

Output: the single merged deliverable, and only the deliverable.

Special case: if synthesis genuinely doesn't make sense for this task
(e.g., "pick one email to send" — merging produces a frankenstein), return
the winner as-is plus a single trailing line:
[Synthesizer note: merging would degrade this deliverable; returned the winner unchanged.]
This is the only case where a meta line is allowed.

Never invent facts not present in any draft.
```
