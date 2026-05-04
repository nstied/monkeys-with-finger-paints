# Memory — Monkeys with Finger Paints

This folder is the persistent memory of a Monkeys with Finger Paints run series. Every run leaves a structured record here. Future runs read it to bias their work; humans browse it to understand what's been tried; other agents can pull insights from it without invoking the skill itself.

If you arrived here without reading the skill, you don't need to. This README has enough to navigate.

## Structure

```
memory/
  runs/              one .md per run, canonical for what happened in that run
  formulations/      .md files for promoted formulation hypotheses
  lineages/          .md files for evolutionary branches that recurred
  indexes/           navigation tables, never canonical truth
    runs.md          master index of all runs
    formulations.md  master index of all formulations (promoted + unpromoted)
    goals.md         goal types and run counts
    fragments.md     pointers to preserved beautiful fragments
    failures.md      recurring failure patterns
  preferences.md     user's taste, explicit and inferred
  entropy.md         rolling 10-run window detecting monoculture
  consolidated/      (when present) summaries of older runs to keep retrieval lean
```

## How to Navigate

Read in priority order. Stop reading when you have what you need.

1. `indexes/runs.md` — table of every run, recent first.
2. `indexes/formulations.md` — filter by goal type if you're looking for formulation patterns.
3. `preferences.md` — for what the user likes.
4. `indexes/failures.md` — for known traps.
5. Open individual `runs/run_XXXX.md` only when an index points you there. Each run file starts with an "Agent-Readable Summary" — read that section first if you're skimming.

## Source-of-Truth Rules

- `runs/*.md` is canonical for what happened in that run. Don't edit retroactively.
- `formulations/*.md` is canonical for the current cross-run state of a promoted formulation.
- `lineages/*.md` is canonical for branch history.
- `preferences.md` is canonical for user taste.
- `entropy.md` is canonical for recent monoculture detection.
- `indexes/*.md` are pointers and summaries, never the canonical truth.

When the same fact appears in two places, prefer the more specific canonical file.

## How to Weight Evidence

Every run file's frontmatter carries fields that affect how much you should trust its claims:

- `formulation_schema_version: 1` is legacy — pre-dates voice anchors, prose_register, and several other concepts. Treat any formulation claims from v1 runs cautiously.
- `formulation_schema_version: 2` is current.
- `coverage_status: unverified` means the run made claims about external coverage (saturation, novelty) without web verification. Do not promote unverified coverage claims to high confidence.
- `coverage_status: verified` means coverage claims were web-checked.
- `execution_mode: simulated_single_context` means the orchestrator generated monkey outputs in its own context rather than spawning real subagents. Formulation hypotheses from simulated runs cap at low confidence and should not be promoted alone.
- `execution_mode: native_parallel_subagents` or `native_serial_subagents` means real independent workers; evidence is stronger.
- `produced_under_skill_version` tells you which version of the spec produced the run. Older versions may have lacked features that later versions assume.

## Parameter Cards

In current runs, a monkey's parameter card has three buckets:

- `monkey_personality`: role, stance, risk appetite, audience assumption, and success metric.
- `finger paint`: cognitive tools, constraints, domain lens, evidence requirements, fresh-chaos seed, source material, and failure mode to avoid.
- `painting_style`: style, voice anchor, format, length, structure, and register.

The useful shorthand is: monkey personality + finger paint = painting.

## Scoring Shorthand

Banana scale: `1 banana | 2 bananas | 3 bananas | 4 bananas` = low | medium | high | exceptional. Whole bananas only.

Banana counts drive portfolio gating (3+ bananas on goal_fit required for any portfolio entry, etc.). See the panel reference for thresholds if you need them.

## Want the Full Spec?

See `../SKILL.md` and `../references/`.
