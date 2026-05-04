---
name: monkeys-with-finger-paints
description: A chaos-preserving evolutionary ideation skill. Puts many independent monkeys on the same goal, each with a different personality-and-finger-paint parameter card, has an adversarial panel pick a portfolio of winners, and preserves results in a Markdown memory that biases future runs. Scales from Sketch (4 monkeys) to Deep Troop (48). Supports Loop Mode (multiple back-to-back runs that compound the memory corpus) and Memory Reflection (the skill reads its own memory corpus and reports what it has learned). Use for ideation, naming, positioning, framing, copywriting, voice, strategy, or radical creative exploration where novelty and beauty matter as much as correctness. Trigger on "monkeys with finger paint", "throw a troop at this", "sketch monkeys", "gallery mode", "deep troop", "loop the monkeys", "go wild on this", "max chaos", "radical ideation", "reflect on memory", "memory check", or "monkeys help". Full invocation catalog in SKILL.md.
license: MIT
metadata:
  author: Nicholas Tiedemann
  version: 0.4.4
---

# Monkeys with Finger Paints

## Purpose

Imagine a room full of monkeys equipped with finger paint. Each monkey gets the same goal, a different personality, different finger paint, and enough privacy to make a real mess.

One might spread feces on the wall. Most will splatter paint everywhere. But one might produce the Mona Lisa.

A judging panel walks in afterward, throws away the failures, and preserves the rare flashes of genuine creativity. The formulations that produced the best paintings are kept in memory, so the next batch of monkeys can be slightly better-equipped to surprise the panel.

This skill does that, except the monkeys are subagents, their personalities are role/stance parameters, the finger paint is the working material and constraints, and the painting is the candidate output. Use it for ideation, naming, positioning, writing, strategy, design, or any task where novelty and beauty matter as much as correctness. Humans set the goal and calibrate taste; the monkeys handle the rest.

This is a skill, not a plugin or standalone product.

Therefore:

- Run the process inside the current assistant workflow.
- Use Markdown files as the canonical memory format.
- Do not assume a database, background worker, vector store, external service, or MCP server.
- Optional helper scripts may exist, but the skill must work without them.
- All memory artifacts must remain human-readable, inspectable, and editable.
- Advanced structures such as lineage graphs, formulation libraries, and preference memory must degrade to plain Markdown.
- Never claim causal certainty from one run. Treat preserved formulations as hypotheses until supported across repeated runs.

The core principle:

> The memory should bias the troop, not determine it.

## When to Use This Skill

Use it for any task where multiple defensible answers exist and taste matters.

Creative work:

- Naming, positioning, framing, taglines, voice, copy.
- Multiple approaches to a writing, design, strategy, or research problem.
- Brand voice and tone exploration.

Coding work:

- Function, module, table, API resource, config flag, feature, and test naming.
- API and resource design — multiple defensible shapes for the same endpoint.
- Error message and log message wording.
- Refactor strategy exploration — same goal, different paths through the code.
- Architecture sketches and migration plans (Postgres-vs-DynamoDB-style decisions).
- Postmortem framings.
- PR descriptions and commit messages for non-trivial changes.
- Documentation voice — README, doc strings, error explanations.

For coding tasks, the orchestrator must include the relevant code context (file content, surrounding code, existing conventions) in each monkey's prompt; without it, monkey outputs will float free of the codebase's style.

The skill will run on whatever you point it at — that is the user's call, not the skill's. Its strengths are creative and strategic ideation, so factual, deterministic, or single-answer questions may give you more stylized takes than you wanted. For high-stakes work (legal, medical, financial, safety-critical) reduce chaos and add evidence/correctness judges; beauty and novelty don't override truth in those domains. Otherwise, the room is open.

## What This Skill Is Not

This skill is sometimes mistaken for *expensive parallel brainstorming*. It is not.

**The brainstorm is the medium. The memory corpus is the product.**

A single run costs more tokens than one careful prompt. But every run leaves notes behind, and by the fiftieth run the room knows — for *this user* on *this kind of goal* — what kinds of weirdness tend to work. That accumulating evidence base is the actual product, not the painting from any one run.

If you read this spec and think "this is just an expensive brainstorming tool," you're looking at one painting and missing the room. Full argument lives in `PITCH.md`.

## What This Skill Produces

Each run produces a synthesized answer plus a portfolio: best overall, beautiful highlights, useful-but-less-beautiful candidates, weird-but-promising branches, failures-with-signal, and false positives. It also produces formulation hypotheses for reuse, user preference notes, lineage updates, and memory writes.

The long-term goal is a searchable memory mapping:

```text
goal → monkey personality + finger paint → painting quality → panel judgment → user preference → future reuse
```

That memory corpus is what lets the skill predict — for *this user*, on *this kind of goal* — what kinds of prompting tend to work.

## Core Concepts

### Monkey

A monkey is one parallel attempt at the user’s goal. Each monkey receives the same base goal but a different parameter card.

### Monkey Personality

Personality is the stance assigned to the monkey for a run: role, temperament, risk appetite, audience assumption, and success metric. This is the part most like "who the monkey is" while painting.

### Finger Paint

Finger Paint is the material the monkey works with: cognitive tools, constraints, domain lenses, evidence requirements, fresh-chaos seeds, and source material. This is the part most like "what the monkey has in its hands."

### Painting

A painting is the candidate output produced by a monkey. Some paintings are useless. Some are beautiful. The panel curates them.

### Formulation

A formulation is a reusable pattern across monkey personality, finger paint, and output style. The seed library and dimension definitions live in `references/formulations.md`.

Metaphor guardrail: the monkey has the personality, the finger paint is the material and constraints, and the painting is the output. Memory is the record of what worked before. Do not rename memory.

### Parameter Card

A parameter card is the structured record of what is assigned to a monkey for one attempt.

A parameter card may include three kinds of settings:

- **Monkey personality parameters:** role, stance, risk appetite, audience assumption, and success metric. These define what kind of monkey is painting.
- **Finger Paint parameters:** cognitive strategy, constraints, domain lens, evidence requirements, fresh-chaos seed, source material, and failure mode to avoid. These define what the monkey has to work with.
- **Painting-style parameters:** style, voice anchor, format, length, structure, and register. These define what the finished painting should look and sound like.

### Formulation Hypothesis

A formulation hypothesis is a preserved guess that a particular instruction pattern contributed to a strong output.

Use cautious language:

- “plausible contributor”
- “candidate formulation”
- “formulation hypothesis”
- “supported pattern”
- “high-confidence pattern”

Avoid unsupported causal claims such as “this formulation caused the result” after one run.

### Panel

The panel is an adversarial judging process. It should not merely average scores. It should debate candidates and select a portfolio.

### User Judge

The user may optionally judge outputs. User judgments are first-class selection signal and should be preserved when the user wants memory persistence.

### Memory

Memory is the file-backed persistent state of the skill. Markdown is the source of truth.

### Lineage

A lineage is a branch of related formulations and outputs across runs. Lineage can diverge, converge, rebranch, recombine, become dormant, or dead-end.

### Fresh Chaos

Fresh chaos is the portion of a run that is not based on prior winners. It keeps the system from becoming tame, repetitive, or overfit.

Fresh chaos should never be zero in creative runs.

## Run Modes

Pick the smallest room that fits the work. More monkeys cost more time and tokens; the room knows when it's getting crowded. If the user specifies a mode, honor it. If not, default to Standard for meaningful creative or strategic work.

### Sketch Mode

Use when the user wants fast ideation or a lightweight trial.

```yaml
monkeys: 4
judges: 2
panel: Curator + Operator or Curator + Skeptic
debate: none or very brief
memory: optional compact run note
raw_attempts: do not preserve by default
```

Sketch under `execution_mode: simulated_single_context` is the one case where the "raw_attempts: do not preserve" default and the "simulation requires per-monkey scratchpads" rule collide. Resolution: under simulated execution Sketch *temporarily* writes per-monkey scratchpads to `monkey-memory/runs/run_<slug>_raw/` for context management, then deletes the `_raw/` directory at the end of the run. The principle holds — Sketch doesn't keep raw attempts — but the orchestrator gets the scratchpad it needs while running.

### Standard Mode

Default for most meaningful uses.

```yaml
monkeys: 8
judges: 3
panel: Curator + Operator + Skeptic
debate: finalist-only champion/critic pass
memory: one run file + index update when persistence is available
raw_attempts: summaries by default; selected raw outputs only
fresh_chaos_ratio: 25-40%
```

### Gallery Mode

Use when the user wants deeper creative exploration or high-quality selection.

```yaml
monkeys: 16-24
judges: 4-5
panel: Curator + Operator + Skeptic + Memory Keeper + Chaos Guardian (mandatory)
debate: adversarial finalist debate
memory: full run file, formulation updates, preference notes, optional lineage notes
raw_attempts: summaries by default; preserve selected raw attempts
fresh_chaos_ratio: 25-50%
```

Chaos Guardian is required in Gallery — the explore-harder tier needs a judge whose explicit job is preventing convergence.

### Deep Troop Mode

Use only when explicitly requested or when the user accepts a heavier process. Deep Troop is bounded by the count policy below.

```yaml
monkeys: 25-48
judges: expanded panel
panel: at minimum Curator + Operator + Skeptic + Memory Keeper + Chaos Guardian (mandatory) + Novelty Judge
debate: multi-stage finalist debate
memory: full update including lineages and formulation library
raw_attempts: optional; summaries remain working memory
fresh_chaos_ratio: 20-50%
```

Chaos Guardian is required in Deep Troop. At this scale, without an explicit anti-convergence judge, the panel will quietly settle on whatever the troop produced most fluently.

### Research Mode

Use for factual or evidence-heavy tasks. This is not necessarily large; it is more rigorous.

```yaml
monkeys: 4-12
judges: Evidence Judge + Skeptic + Synthesis Judge + optional Domain Judge
chaos: low to medium
requirements: citations, uncertainty, source quality, factual checks
beauty: secondary to correctness
```

## Default Behavior

Unless the user specifies otherwise:

1. Use Standard Mode.
2. Generate 8 monkeys.
3. Reserve at least 2 monkeys for fresh chaos.
4. **Weirdness floor:** at least one monkey in every Standard+ run carries `risk_level: bizarre` or higher. Verify this when planning the troop. Without it, the troop's "diversity" is just conservative variation and the run will quietly converge on safe outputs over many invocations. See the `tameness_drift` failure pattern in references/quality.md.
5. Use Curator, Operator, and Skeptic judges. (Add Codesmell for coding-flavored tasks. Add Humanizer for prose-register tasks. Chaos Guardian is mandatory in Gallery and Deep Troop.)
6. Select a portfolio, not a single winner.
7. Preserve formulation hypotheses cautiously.
8. Ask for user judging only when it materially improves the run or when the user has requested preference preservation.
9. If memory files are available, use them. If not, run fresh and create a first-run record when appropriate.

### User Control of Monkey Count

The user can specify any integer; the orchestrator honors it within operational bounds. Default is 8.

```yaml
count_policy:
  default: 8
  no_confirmation_required: 1-12
  confirmation_required: 13-24
  explicit_deep_troop_required: 25-48
  hard_cap: 48
```

Natural-language examples that set the count: *"Use 30 monkeys for this."* *"Sketch — 4 monkeys."* *"Just give me 6."*

Above 48, treat as Deep Troop intent and cap at 48; tell the user the hard cap unless the host environment has a project-specific override.

When count alone is given, infer mode from count: 1-6 → Sketch-like, 7-14 → Standard-like, 15-24 → Gallery-like, 25-48 → Deep Troop-like. Panel size, debate depth, and persistence richness scale with the inferred mode. Fresh chaos floor (≥25%), weirdness floor (≥1 bizarre+ monkey), and dimension diversity hold at any count.

When mode alone is given, use the mode's default count. When both are given, honor both. Don't ask for a count when the default applies.

## Memory Location

Where memory gets written matters. The wrong default leaks one project's evidence into another and erodes the memory's value. The skill's policy:

**Default: per-project memory.** The first time the skill runs in a working directory, it creates `monkey-memory/` inside that working directory. All run files, formulation files, lineages, indexes, and the project's own preferences live there. This keeps each project's memory isolated.

**Optional: shared memory across projects.** Some signals are about the user, not the project — voice preferences, AI-speak rejection, blunt-vs-polite, how the user wants the panel to talk. Those don't belong locked inside one project. The user can opt into a shared memory location that persists across projects.

**First-run procedure (per project).** The first time the skill runs in a new working directory:

1. Check whether `monkey-memory/` is already present. If yes, skip the orientation entirely — the user has been here before.
2. Check whether a shared memory location exists at `~/.monkeys-with-finger-paints/shared/`. If not, this is the user's first run anywhere, and the path can be bootstrapped on opt-in.
3. **Surface the orientation block** (see "First-Run Orientation" below) — short skill intro plus the knobs the user can reach for.
4. **Ask the shared-memory question** as part of the same message: *"Want a shared preferences memory across all your projects? (y/n)"*
5. Record both the answer and `oriented: true` in `monkey-memory/.config.yaml`. Sticky — orientation never fires again for this project.
6. Then proceed with the user's actual request.

### First-Run Orientation

The orientation block fires once per project (sticky after) and on demand whenever the user invokes `monkeys help` or similar (see Skill Invocation Examples). It is not run-mode-specific — same content every time. Tone: warm, brief, monkey-flavored, no condescension.

Standard text:

> *🐵 Welcome. I'm Monkeys with Finger Paints. Here's the deal: I send a room of monkeys at your goal, a panel picks the portfolio, I keep notes in `monkey-memory/` for next time.*
>
> **Defaults if you say nothing:** 8 monkeys, Standard panel (Curator + Operator + Skeptic), I pick the winners.
>
> **Knobs you can pull just by saying the words:**
> - `sketch` → 4 monkeys, faster
> - `gallery` → 16 monkeys, deeper
> - `loop 5 times` → back-to-back runs that compound the memory
> - `I'll judge` → you're the final selector
> - `go wild` / `max chaos` → bias toward weirder paint
> - `reflect on memory` → I read the memory and tell you what it's learned (no new run)
>
> **One question for you now:** want a shared preferences memory across all your projects? (y/n)

After the user answers the shared-memory question, the skill records both `oriented: true` and `shared_memory_enabled: true|false` in `.config.yaml`, then runs the user's original request.

**On-demand re-orientation.** If the user types `monkeys help`, `what can you do`, or any clear request for the skill's options at any later point, surface the orientation block again — but skip the shared-memory question (their answer is already recorded). This is reading, not running. No new troop, no panel, no run file.

### Returning-User Prompt

When the skill is invoked into a project where orientation has already happened (`monkey-memory/.config.yaml` has `oriented: true`) AND the user hasn't given a goal, the skill needs to ask what they want. This is where it's easy to forget that the memory has been growing — and where the user is most likely to forget too.

Branch by memory size, count run files in `monkey-memory/runs/`:

- **0–2 runs** in memory → just ask for the goal as today: *"What paint? What do you want the monkeys to make?"*
- **3–8 runs** → mention the memory alongside the goal request: *"What's the goal? You've got [N] runs in this project — say `memory check` if you want a quick read of what the room has learned before adding another."*
- **9+ runs** → suggest reflection more strongly, since there's enough history to make it informative: *"What's the goal? You've got [N] runs in this project — that's enough memory to be worth a check before the next run. Say `memory check` for a quick read, or give me the goal and I'll go."*

The trigger phrase is `memory check` (or any of the existing reflection phrases: *"reflect on memory"*, *"what has the room learned"*, etc.). When fired, route to the Memory Reflection Procedure in `references/persistence.md`. Don't run a new troop until the user comes back with a goal.

**Why this matters:** without a returning-user prompt, the skill silently treats invocation #2 the same as invocation #200 — both get an empty-handed "give me a goal" request. The memory only earns its keep if the skill volunteers it at the moments where it's relevant.

`.config.yaml` content:

```yaml
schema_version: 1
project_memory_path: monkey-memory/
shared_memory_enabled: true | false
shared_memory_path: ~/.monkeys-with-finger-paints/shared/
oriented: true            # set after first-run orientation; suppresses re-orientation
created: YYYY-MM-DD
```

If the user says no, `shared_memory_enabled: false` and nothing leaves the project. If yes, the rules in the next subsection govern what crosses the boundary.

### What Goes Where When Shared Memory Is Enabled

Cross-contamination — applying whiskey-naming taste to a coding decision, or applying coding taste to a whiskey naming task — is the failure mode this guards against. The shape of the guard, in one paragraph:

**HOW-preferences** (stylistic, cognitive, or register preferences that hold across domains — voice, length, blunt-vs-polite, AI-speak rejection) are eligible for promotion to shared memory after they're observed in **2+ different projects**. **WHAT-preferences** (anything tied to a specific domain, vocabulary, technology, or subject matter — including engineering-taste-in-code) stay per-project, never shared. When classifying, when in doubt: WHAT. **Run files, formulation files, lineages, indexes, highlights, failures, entropy memory** all stay per-project regardless of the shared flag — they belong to the project. **User override always wins** — if the user says "stop applying this preference here" or overrides a panel call influenced by a shared preference, the preference's confidence drops a tier and may be evicted.

Full detail — promotion procedure, match algorithm, cross-project registry, mid-project flip behavior, cap/decay/consolidation rules, override handling, write-failure procedure — lives in [`references/persistence.md`](references/persistence.md) under "Shared Preferences — What Crosses, What Doesn't" and the sections that follow.

## Subagent Execution

Monkeys work alone. That is the entire point of the room being full. Each monkey gets the same goal, a different personality, different finger paint, and none of them see what the others are making. Independence is what produces genuinely different paintings; without it, the troop quietly converges on whatever the model produces most fluently and the run becomes louder brainstorming, not exploration.

Use real independent workers (Task subagents, agent spawns, whatever the host runtime calls them) whenever available. Single-context simulation collapses the independence and weakens every claim downstream — use it only as a documented degraded execution mode.

The three execution modes are `native_parallel_subagents` (preferred), `native_serial_subagents` (acceptable degraded), and `simulated_single_context` (last resort — simulated runs can't promote formulation confidence above low from that run alone). Detail on each mode, runtime adapters (Claude Code / Codex / Antigravity), how monkeys are spawned, how judges run, cost and latency math, simulated-execution scratchpad rules, and orchestrator responsibilities all live in [`references/execution.md`](references/execution.md). Read it at Steps 5–7 (running the monkeys, prepping candidates, panel judging) or whenever the runtime adapter or execution mode is in question.

## First-Read Procedure

When invoked, load context in this order. Stop reading lower tiers once you have enough to act. The point is to never put the whole spec into context — only the parts the current run needs.

### Always (every invocation)

1. `SKILL.md` (this file) — runtime procedure and principles.
2. `references/formulations.md` — only the dimensions and seed formulations relevant to the current goal type and run mode.
3. `references/panel.md` — only the judge roles in the current panel composition.
4. `monkey-memory/.config.yaml` — if present, tells the orchestrator whether shared memory is enabled and where it lives. If absent, the skill is in a new working directory and must run the first-run setup (see Memory Location section).

### When `monkey-memory/` exists (mature runs)

5. `monkey-memory/indexes/runs.md` — last 10 rows only.
6. `monkey-memory/indexes/formulations.md` — filter by current goal type.
7. `monkey-memory/preferences.md` — full file (it stays small).
8. If `shared_memory_enabled: true` in `.config.yaml`, also load `~/.monkeys-with-finger-paints/shared/preferences.md` — full file.
9. `monkey-memory/entropy.md` — full file (rolling 10-run window).
10. One to three most relevant prior runs — load only the `Agent-Readable Summary` section, never the full run file.

### Only if needed

Grouped by what you're trying to do. Read the file(s) under the matching row.

- **Running monkeys / panel judging (Steps 5–7):** `references/execution.md`.
- **Synthesizing the final response (Step 9):** `references/response-style.md`.
- **Writing the run file or persisting results (Step 10):** `references/persistence.md`.
- **Loop Mode invoked or being continued:** `references/loop-mode.md` (and `monkey-memory/loops/<slug>.md` for the in-progress wrapper).
- **First-run project setup:** `references/persistence.md` (Memory Location, Run ID Generation sections).
- **Memory reflection (user asked for memory synthesis, no new run):** `references/persistence.md` (Memory Reflection Procedure section).
- **A discipline check fires** (verification, causality, beauty, failure pattern, weirdness floor): `references/quality.md`.
- **Specific formulation lookups:** the named files in `monkey-memory/formulations/` (only the ones that appeared in step 6).
- **Rebranching or auditing a prior run:** the full run file from `monkey-memory/runs/`. Raw attempts in `monkey-memory/runs/<slug>_raw/` only when exact phrasing matters.

### Retrieval restraint

Don't load every prior run file. Indexes plus a handful of relevant agent-readable summaries are usually enough. If you're opening more than a few full run files for a single new run, stop and ask whether the rest of those reads will inform planning.

## High-Level Workflow

### Step 1: Interpret the Goal

Identify:

- User goal.
- Desired output type.
- Goal type.
- Constraints.
- Whether the task is creative, technical, factual, strategic, or mixed.
- **Prose register**: will the user-facing output be read as natural-language prose by a human (email copy, taglines, names, blog drafts, social posts, ad copy, headlines, written ideation, brand voice) or as structured/technical content (code, system designs, research synthesis, planning docs, tables)? When prose register is true, the panel adds the Humanizer judge, the Skeptic activates its prose-task checklist, and at least one monkey carries a voice-anchor paint (see Voice Anchor under Formulation Generation).
- Appropriate run mode.
- Appropriate judge panel.
- Whether memory retrieval is available.
- Whether user judging should be invited.

Use a fixed goal taxonomy by default:

```yaml
goal_types:
  - ideation
  - naming
  - product_strategy
  - positioning
  - writing
  - technical_design
  - research
  - planning
  - critique
  - synthesis
  - implementation
  - evaluation
```

If uncertain, assign the closest goal type and mark confidence:

```yaml
goal_type: product_strategy
subtype: positioning
confidence: medium
```

Do not block the run for minor classification uncertainty. Proceed with a best-effort classification and let the user correct it later.

### Step 2: Retrieve Prior Memory Context

If memory files exist, retrieve compact context before generating monkeys.

Search order:

1. `monkey-memory/indexes/runs.md`
2. `monkey-memory/indexes/formulations.md`
3. `monkey-memory/indexes/goals.md`
4. `monkey-memory/preferences.md`
5. Relevant files in `monkey-memory/formulations/`
6. Relevant files in `monkey-memory/runs/`, summaries only
7. Relevant files in `monkey-memory/lineages/`
8. Raw attempts only if rebranching or auditing

Prefer compact summaries and retrieval capsules. Avoid loading entire raw logs unless needed.

If no memory exists, use seed formulations and fresh chaos.

### Step 3: Plan the Troop

Choose monkey count and formulation mix based on run mode. Two principles govern the plan: *source diversity* and *dimension diversity*.

**Source diversity** — where each monkey's formulation comes from. A Standard Mode troop should include:

- 2 monkeys using relevant seed or supported formulations.
- 2 monkeys using nearby variants.
- 2 monkeys using crossbreeds or contrasting strategies.
- 2 monkeys using fresh chaos.

**Dimension diversity** — which formulation axis each monkey explores at the edge. Each monkey carries one *unusual vector* — the dimension where its parameter card diverges sharply from a default parameter card — while the other dimensions stay closer to seed values. Across the troop, the unusual vector should be spread across different dimensions, not stacked on the same one.

Concretely: if eight monkeys all have wild Cognitive Strategies but seed-typical Roles, Styles, Constraints, and Voice Anchors, the troop is less diverse than it looks. The output will be correlated. Better: each monkey is doing one weird thing well, on a *different* dimension, not eight cosmetically-different things.

Rule of thumb for Standard Mode: no two monkeys should share their unusual vector. The seven non-Risk-Level dimensions plus a "fresh chaos" slot give comfortable coverage for eight monkeys.

Example:

```yaml
troop_plan:
  run_mode: standard
  monkeys: 8
  source_mix:
    seed_or_supported: 2
    nearby_variants: 2
    crossbreeds: 2
    fresh_chaos: 2
  dimension_diversity:
    monkey_01_unusual_vector: role
    monkey_02_unusual_vector: cognitive_strategy
    monkey_03_unusual_vector: style
    monkey_04_unusual_vector: constraint
    monkey_05_unusual_vector: success_metric
    monkey_06_unusual_vector: voice_anchor   # only if prose register
    monkey_07_unusual_vector: risk_level     # bumped to bizarre or alien
    monkey_08_unusual_vector: fresh_chaos    # multiple unusual axes
```

### Step 4: Generate Parameter Cards

For each monkey, define a compact parameter card with all dimensions filled in. One dimension is the *unusual vector* (sharp divergence from default); the others are seed-typical.

Example showing dimension diversity across three monkeys for a positioning task:

```yaml
monkey_03:
  unusual_vector: constraint
  role: skeptical operator                          # seed-typical
  cognitive_strategy: [failure_mode_first]          # seed-typical
  style: direct product memo                        # seed-typical
  risk_level: plausible                             # seed-typical
  constraint: every sentence must contain a number or a proper noun  # UNUSUAL (invented)
  success_metric: usefulness                        # seed-typical
  voice_anchor: none                                # n/a — task is structured

monkey_04:
  unusual_vector: voice_anchor
  role: customer advocate                           # seed-typical
  cognitive_strategy: [customer_backwards]          # seed-typical
  style: founder memo                               # seed-typical
  risk_level: plausible                             # seed-typical
  constraint: optimize for memorability    # seed-typical
  success_metric: emotional resonance               # seed-typical
  voice_anchor: "Selina Meyer in a moment of brief sincerity"  # UNUSUAL (invented)

monkey_05:
  unusual_vector: role
  role: an internal critic from Google's Brand Council in 2014  # UNUSUAL (invented)
  cognitive_strategy: [first_principles]            # seed-typical
  style: terse memo                                 # seed-typical
  risk_level: bold                                  # seed-typical
  constraint: assume no demo is possible   # seed-typical
  success_metric: clarity                           # seed-typical
  voice_anchor: none
```

Each parameter card explicitly names its `unusual_vector` so the Memory Keeper can later reason about which dimension carried the win and the failure. Parameter cards should remain short enough to preserve and compare across runs.

### Step 5: Run the Monkeys

Spawn the monkey workers per the Subagent Execution rules above (each receives goal, shared constraints, parameter card, and output requirements). Keep each monkey's output focused — don't let one consume the whole context window.

**Pre-run troop introduction (user-facing).** Before the workers run, surface a one-line stage-setting note that names each monkey and what makes it distinct. Helps the user remember who's who when the panel reports back. **Always include the canonical ID alongside each name** — names without IDs in the intro tend to leak into later prose as the only identifier, which is how compressed bug-IDs like `P2` get invented downstream. Format:

> *"🐵 Recruiting Monkey 01 / Bonzo (failure-mode-first), Monkey 02 / Rita (patio11-voiced), Monkey 03 / Mojo (alien metaphor + headline-derived constraint), Monkey 04 / Banana Republic (Sheldon-Cooper voice), Monkey 05 / Chimp Chomsky (recursive constraint), Monkey 06 / Pickles (founder-memo), Monkey 07 / Dr. Bananas (skeptical-investor), and Monkey 08 / Gerald (fresh chaos seed: today's NYT homepage). Painting now."*

**Invent fresh, monkey-themed names every run.** Don't reuse Bonzo/Rita/Mojo/etc. as a recurring cast — each run gets new names that reflect *that run's* parameter cards. Lean into the theme: monkey-flavored, banana-flavored, primate-adjacent, jungle, paint-related, slightly silly. The IDs (`monkey_01`-`monkey_08`) are the canonical link to memory; the names are run-local color.

For Sketch mode, a shorter version: *"Four monkeys: Monkey 01 / [name] (tag), Monkey 02 / [name] (tag), Monkey 03 / [name] (tag), Monkey 04 / [name] (tag). Painting."*

**Mid-run status (Gallery and Deep Troop only).** Long runs have a real wait. Surface one short atmospheric line between major phases:

> *"Eight monkeys have been finger-painting a while. Will brilliance emerge?"*
> *"Sixteen monkeys done painting. Panel walking in to judge."*
> *"Half the troop has set their brushes down. The other half is still making a mess."*
> *"The room smells like paint. Final monkeys finishing up."*

Mix registers — anticipation, procedural, atmospheric. Pick the line that fits this run's progress; don't keep one tone across every run. Don't narrate every step; one line per major phase is enough. Don't claim activity that hasn't happened (no "the Curator made a face" before the panel has run).

### Step 6: Prepare Candidates for Judging

Before judging:

- Summarize each output.
- Extract notable highlights.
- Hide monkey IDs and parameter cards for first-pass judging when practical.
- Normalize candidates into comparable format.
- Flag unsupported factual claims if relevant.

Candidate card format:

```markdown
### Candidate A

Summary:

Best highlight:

Strengths:

Risks:

Potential category:
- best overall
- beautiful highlight
- useful but plain
- weird branch
- false positive
- failure with signal
```

### Step 7: Panel Judging

Use the panel process described below.

### Step 8: User Judging — Adaptive Closing Modes

The user always has the right to interject in any run, at any point — nominate a different finalist, ask the panel to re-judge with a different criterion, mark a candidate as a false positive, change panel composition mid-stream, request a rebranch. This right is permanent and does not depend on what the orchestrator does at end-of-run. The only thing the orchestrator decides is whether to *pause* and explicitly invite override.

Default to **not asking** every run — repetitive override prompts are annoying. Pick the closing mode based on what the run produced:

**Clean close (most runs).** Panel was decisive, no strong runner-up, no genuine disagreement. Deliver the synthesis. End with the "Next moves" footer (see Step 9). No override question. The user knows they can interject if they want.

**Split-panel close.** Judges genuinely disagreed — Curator wanted A, Operator wanted B, Skeptic threatened both. Surface the disagreement plainly: *"The Curator wanted [A]; the Operator wanted [B]. Panel went with [B]. Worth your tiebreaker, or stick with the call?"* The override question is earned by actual division.

**Strong-runner-up close.** A non-winner scored almost as high on a different axis (e.g., 4-banana beauty but only 2-banana goal-fit). Surface the alternative: *"[Best Overall] won. [Runner-up] hit beauty hard but missed goal-fit by a banana. Worth a second look or move on?"* Asks because there's a genuine alternative path.

**Preference-learned frequency.** `monkey-memory/preferences.md` tracks override rate across recent runs:

```yaml
override_history:
  total_runs: 0
  overrides_applied: 0
  override_rate: 0.0
  window: last_10_runs
```

Trigger logic:

- `override_rate > 0.3` across the last 10 runs → bias toward Strong-runner-up close (treat marginal runner-ups as worth surfacing).
- `override_rate < 0.1` across the last 10 runs → bias toward Clean close (don't ask unless the panel was genuinely split).
- Between 0.1 and 0.3 → use whichever close the run warrants (no preference bias).

Each time the user overrides the panel's call, increment `overrides_applied` and recompute `override_rate`. Each new run increments `total_runs`. Adaptive over time.

### Step 9: Synthesize Final Output

Produce a final answer from the portfolio and present it using the **canonical run-output template** defined in [`references/response-style.md`](references/response-style.md) → "Canonical Run-Output Template". The template skeleton is fixed: headline synthesis → what the monkeys made → what the panel said (with one of four verdict shapes) → the portfolio → next moves → memory updates. Voice and tone rules for filling each section, plus Sketch / Loop / Reflection variants, live in the same file.

Read it now. Don't paste the winning monkey output unless that *is* the best synthesis — the synthesis is usually recombined from the portfolio, not copied.

### Step 10: Persist Results

Persistence depends on run mode. Skipping required persistence silently breaks the system's ability to learn across runs, but lightweight Sketch runs should not be forced into full bookkeeping.

```yaml
persistence_by_mode:
  sketch:
    required: false
    default: compact run note only when memory exists and the request is not clearly disposable
    indexes: optional
  standard:
    required: true
    default: run file + core indexes
  gallery:
    required: true
    default: full run file + indexes + preference/formulation updates
  deep_troop:
    required: true
    default: full persistence + lineage/entropy updates when applicable
  research:
    required: true
    default: run file + evidence/uncertainty notes + core indexes
```

For Standard Mode or deeper, treat each applicable substep as a hard requirement when file access exists.

**Required writes:**

1. **Allocate the run ID** as a Heroku-style two-word slug: `run_<adjective>_<primate>` (e.g., `run_silent_capuchin`). Pick from the seed lists in `references/persistence.md` → "Run ID Generation" or invent. Check filesystem; if the slug is taken, append `_2`, `_3`, etc. until unique. Then write the run file at `monkey-memory/runs/<slug>.md` using the run template. Include the agent-readable summary, panel decision, formulation hypotheses, highlights, failures, and retrieval capsule. If this run is part of a Loop Mode session, set `loop_session` and `loop_position` in the frontmatter.

2. Append a row to `monkey-memory/indexes/runs.md` with the run ID, date, goal type, mode, best formulations, user preference signal, canonical run file path, and notes.

3. For each formulation hypothesis identified by the panel:
   - Append or update a row in `monkey-memory/indexes/formulations.md` with `Promotion: unpromoted` if first occurrence, or update evidence count if already present.
   - If any formulation crosses a promotion threshold (see Promotion Thresholds section), create or update its `monkey-memory/formulations/[name].md` file.

4. Update `monkey-memory/indexes/goals.md` — increment count for the goal type, or add a new row if first occurrence.

5. If a failure-with-signal was identified, append to `monkey-memory/indexes/failures.md`.

6. If the panel selected a Best Highlight, append a pointer row to `monkey-memory/indexes/highlights.md` (canonical text stays in the run file).

7. If user preferences were observed and meet the preference promotion threshold, update `monkey-memory/preferences.md`. Classify each new entry as **HOW** or **WHAT** per the Memory Location section. For HOW entries, increment `cross_project_observation_count` and check shared promotion (next step).

8. If `shared_memory_enabled: true` and any HOW-preference observed in this run has now appeared in **2+ projects**, promote it to `~/.monkeys-with-finger-paints/shared/preferences.md` and update `~/.monkeys-with-finger-paints/shared/.meta.yaml`. Run cap/decay/consolidation checks per `references/persistence.md`. Never promote WHAT-preferences. **If the shared write fails** (permissions, missing dir, readonly FS, disk full), do not silently swallow the error — surface the failure to the user, name the specific error, and ask how to handle it: retry, skip just this entry, or disable shared memory for the rest of the session. The run itself is still considered complete.

9. If a lineage threshold was crossed, create or update the lineage file in `monkey-memory/lineages/`.

10. **If this run is part of a Loop Mode session**, also append per-loop synthesis row to the session's `monkey-memory/loops/loop_<slug>.md` and update the session's memory-movement section. The wrapper file is finalized only at session end (or interrupt).

For Standard Mode or deeper, the run is not complete until all applicable writes above have happened. The orchestrator should explicitly checkpoint each: "run file written → runs index updated → formulations index updated → goals index updated → preferences updated → shared check → loop wrapper updated → ..."

**Where to write.** Top-level `outputs/` is for user-facing artifacts only — `final_response.md` (or equivalent). Everything else lives under `monkey-memory/`. No `EXECUTION_SUMMARY.md`, no freelance status reports, no ad-hoc metadata at the top level. Per-monkey scratchpads (when simulation requires them) go to `monkey-memory/runs/run_<slug>_raw/`, governed by the Raw Attempts policy in references/persistence.md.

If persistence is not available, include a compact run record in the response and note that index updates were skipped.

## Formulation Generation

Detail lives in [`references/formulations.md`](references/formulations.md). That file covers:

- The seven formulation dimensions (Role, Cognitive Strategy, Style, Risk Level, Constraint, Success Metric, Voice Anchor) and how to compose them.
- The seed formulation library (~20 named seeds with instruction patches).
- Voice Anchor sub-library (writers, fictional characters, stated personas, brand voices) plus invention guidance.
- Fresh Chaos Generation — the four-tier procedure (web entropy, memory-negation, deterministic hash, model self-distance) with seed recording format.

Read it at Step 3 (Plan the Troop) and Step 4 (Generate Parameter Cards).

## Panel Design and Judging

Detail lives in [`references/panel.md`](references/panel.md). That file covers:

- Core judges (Curator, Operator, Skeptic) and the conditional Humanizer for prose-register tasks.
- Expanded judges (Memory Keeper, Chaos Guardian, Goal Advocate, Novelty Judge) for Gallery and Deep Troop modes.
- Specialized judges by domain (code, research, naming, product strategy, writing).
- Judging procedure — blind first pass (banana scoring), nominations, adversarial debate, revised judgment, portfolio selection, parameter-card reveal.
- Prompt templates for the monkey, each judge, and the panel decision.

Read it at Step 6 (Prepare Candidates), Step 7 (Panel Judging), and Step 9 (Synthesize).

## User Judging

User judgment is optional but important — preferences are the real selection pressure when work is subjective. *When* to invite the user is governed by Step 8 (Adaptive Closing Modes). The roles the user can play and how preferences get captured live below.

### User Judge Modes

The user can act as:

- Final judge: chooses the winner.
- Taste calibrator: marks liked and disliked patterns.
- Formulation editor: says which instruction pattern to preserve.
- Rebranch director: chooses an old branch to explore.
- Panel override: rejects the panel's choice.

**Trigger phrases for "I want to be the final judge" mode** (set at invocation time): *"I'll judge"*, *"I want to judge"*, *"let me pick"*, *"I'm the final judge"*, *"I'll decide"*. When any of these fire, the orchestrator runs the troop and the panel scores normally — but the user-facing output uses the **User Judge Mode variant** of the canonical run-output template, defined in [`references/response-style.md`](references/response-style.md). That variant: shows every monkey's full output (or rich summary if long), shows the panel's banana scores as reference, surfaces the explicit judging criteria (goal-fit / beauty / usefulness / surprise), and offers clear response options (pick by category, override the panel, narrate freely, or request a rerun). Do not pick a Best Overall before the user responds. Once the user picks, persist their choices as the canonical Panel Decision and increment the override counter in `monkey-memory/preferences.md` for any pick that diverged from the panel's top score.

### Post-Run Critique Handling

When the user critiques the output after the run is complete (panel already picked, run file already written):

1. **Distill** the critique to its signal — one or two sentences capturing what the user is calling out. Don't paste verbatim; the user's prose will be loose. Append the distillation to the run file's `## User Judgment` section, dated.
2. Save it as a preference in `monkey-memory/preferences.md` per the existing HOW/WHAT classification rules. Read the critique like a normal sentence and infer what it means.
3. If the critique is an explicit override of the panel's pick (*"X should have won, not Y"*), increment `override_history`. Otherwise just save and move on.
4. Only ask a clarifying question if the critique is so vague no signal can be extracted. Don't ask routinely.

The original Score Matrix and Panel Decision sections of the run file stay unchanged — those are the historical record. Post-run critique lives in `## User Judgment`.

### Preference Capture

When the user expresses preferences, preserve them if memory persistence is available or requested.

Each preference is classified as either **HOW** (about the user as a writer/thinker — domain-general) or **WHAT** (about this project's subject matter — domain-specific). HOW-preferences are eligible for promotion to a shared memory across projects; WHAT-preferences stay per-project. Promotion to shared requires the preference to appear in 2+ projects. Full mechanics — promotion thresholds, cap, decay, consolidation — live in `references/persistence.md` under "Shared Preferences — What Crosses, What Doesn't".

Mark each entry as `explicit` or `inferred`. The full preferences-file shape lives in the Preferences File Template in `references/persistence.md`. Brief example:

```markdown
# User Preferences (excerpt)

## How-Preferences (Explicit)
- "Likes adversarial critics." (HOW, explicit, high confidence)
- "Reaches for blunt voices, not consultant prose." (HOW, explicit, high confidence)

## How-Preferences (Inferred, Low Confidence)
- "May prefer conceptual elegance over exhaustive completeness." (HOW, inferred, low confidence)

## What-Preferences (Project-Specific)
- "Wants this skill's brand voice to be irreverent." (WHAT, explicit, high confidence)

## Disliked
- "Generic multi-agent framing." (HOW, explicit)
- "Overly methodical serial-process language." (HOW, explicit)
```

Rules:

- User preference files must be human-readable.
- The user must be able to inspect, edit, and delete them.
- Do not infer sensitive preferences aggressively.
- Mark inferred preferences as inferred and low confidence unless repeatedly confirmed.
- Classify HOW vs WHAT before writing. When in doubt, classify as WHAT — keeps shared small and clean.

## Selection Categories

Use these categories to avoid flattening all value into one winner.

### Best Overall

The strongest complete answer.

### Most Beautiful

The result or highlight with the most resonance, compression, memorability, or generative power.

### Most Useful

The most actionable or implementation-ready result.

### Most Surprising

The result that opens the most unexpected but meaningful direction.

### Best Highlight

A phrase, metaphor, frame, or move worth preserving even if the full output is weak.

### Failure with Signal

An attempt that failed but revealed a useful warning, concept, formulation, or branch.

### False Positive

An output that looked strong initially but failed under critique.

### Rebranch Candidate

A result, highlight, formulation, or old lineage worth exploring later.

### Formulation Hypothesis

A preserved instruction pattern that may have contributed to a valuable result.

## Persistence and Retrieval

Detail lives in [`references/persistence.md`](references/persistence.md). That file covers:

- Persistence model and `monkey-memory/` folder structure.
- Source-of-truth hierarchy (run files canonical for events; entity files canonical for promoted state; indexes for navigation only).
- Promotion thresholds (when a formulation/highlight/lineage earns its own file).
- Duplication rules and raw attempts policy.
- Run, formulation, lineage, and index file templates.
- V1 retrieval mechanism, retrieval discipline, retrieval planning summary.
- Memory consolidation procedure for when `monkey-memory/runs/` exceeds ~50 files.

Read it at Step 2 (Retrieve Prior Memory Context) and Step 10 (Persist Results).

## Quality and Discipline Controls

Detail lives in [`references/quality.md`](references/quality.md). That file covers:

- Formulation lifecycle (seed → hypothesis → supported → strong → cooldown → dormant → reactivated/retired).
- Entropy memory (rolling 10-run window tracking source mix, dimension distribution, paint family heat).
- Causality rules (cautious formulation-attribution language; "may have contributed" over "caused").
- Verification discipline (coverage claims like "over-covered" require web verification or `unverified` flagging).
- Beauty rules (banana-scale categorical only; Skeptic challenges beauty).
- Chaos management (anti-convergence, fresh-chaos floor, cooldown of dominant formulations).
- Lineage and rebranching (when to track, how to rebranch, crossbreeding sparingly).
- Failure handling (catalog of failure patterns: polished_consultant_convergence, poetic_fog, clever_irrelevance, overfit_memory_echo, unsupported_confidence, unverified_saturation, ai_speak_giveaway).

Read it whenever a discipline check fires.

## Cost and Effort Controls

Every monkey costs tokens and time. A room with eight monkeys costs less than a room with twenty-four; a sketch costs less than a gallery. Pick the room that fits the work, and don't quietly invoice the user for paint they didn't ask for.

Guidelines:

- Sketch Mode for quick thinking.
- Standard Mode for default creative or strategic work.
- Gallery Mode for important subjective work.
- Deep Troop only when explicitly requested.
- Research Mode for factual tasks.

When the run would be expensive, scale down while preserving the core loop:

```text
varied attempts → adversarial judgment → portfolio selection → preservation
```

## Prompt Templates

See [`references/panel.md`](references/panel.md) — the templates for Monkey, Curator, Operator, Skeptic, Memory Keeper, Chaos Guardian, and Panel Decision live there alongside the panel design.

## Response Style When Running the Skill

The canonical run-output template (section skeleton) AND the voice/tone detail that fills it — voice posture, panel voice, failure-pattern translation to plain-English alarms, lineage callbacks, monkey display naming with canonical-ID rules — both live in [`references/response-style.md`](references/response-style.md).

Read it at Step 9 (Synthesize Final Output). The first principle, kept here: **voice wraps information; it never replaces it.** If a sentence has to choose between flavor and clarity, choose clarity. The reader should walk away knowing what won, why, and what to do next — *and* be entertained on the way through. If they only know they were entertained, the run failed.

## Skill Invocation Examples

The user may invoke the skill with phrases like:

```text
Run Monkeys with Finger Paints on this.
```

```text
Give me a Gallery Mode run.
```

```text
Use 16 monkeys and preserve the formulations.
```

```text
I want to be the final judge.
```

```text
Rebranch from the weirdest promising highlight.
```

```text
Cross the best naming monkey with the skeptical operator.
```

```text
Use prior memory, but keep 40% fresh chaos.
```

```text
Show me the formulation hypotheses from the last run.
```

```text
Loop the monkeys 5 times on this.
```

```text
Reflect on memory — what has the memory learned about my taste?
```

```text
monkeys help
```

The last form re-surfaces the orientation block (knobs catalog) without running a new troop. Useful when the user has forgotten what's available.

## Skill Operating Principles

1. Use memory as a bias, not a cage. Keep randomness alive.
2. Select portfolios, not only winners.
3. Preserve minimal formulation hypotheses, not bloated prompts. Be honest about uncertainty.
4. Track failures as negative knowledge.
5. Treat prompt evolution as accumulated capital — that is the actual product.
6. Let user taste become selection pressure when the user wants that.
7. Scale the run to the user's actual need.

## Minimal First Run

If this skill is used for the first time with no memory, do this:

1. Classify the goal.
2. Choose Standard or Sketch Mode.
3. Select seed formulations.
4. Generate monkeys.
5. Judge with Curator, Operator, and Skeptic.
6. Select a portfolio.
7. Ask the user whether to preserve preferences if subjective.
8. Bootstrap the memory folder structure: `monkey-memory/runs/`, `monkey-memory/formulations/`, `monkey-memory/lineages/`, `monkey-memory/indexes/`, plus header rows in `monkey-memory/indexes/runs.md`, `monkey-memory/indexes/formulations.md`, `monkey-memory/indexes/goals.md`, `monkey-memory/indexes/highlights.md`, `monkey-memory/indexes/failures.md`. Initialize an empty `monkey-memory/entropy.md` (rolling-window placeholder) and `monkey-memory/preferences.md` (only if the user wants preference tracking).
9. **Create `monkey-memory/README.md`** by writing the standard orientation content from the Memory README Template in `references/persistence.md`. This file makes the memory self-explanatory to any agent — including agents that have never read this skill — that opens the `monkey-memory/` folder. It is required, not optional, on first bootstrap.
10. Allocate the run ID as a Heroku-style slug (e.g., `run_silent_capuchin`) per `references/persistence.md` → "Run ID Generation", then write the first run file at `monkey-memory/runs/<slug>.md`.
11. Append the run's row to the relevant indexes per Step 10 of the workflow.

First-run files may be sparse. That is acceptable. Memory compounds over time.

## Mature Run

When memory exists, follow the High-Level Workflow but with retrieval doing real work: pull similar goals, relevant formulations, user preferences, failure patterns, and entropy state before planning the troop. Cool down dominant formulations. Recommend rebranches when an old branch becomes relevant again. Everything else is the same shape as a first run.

## Loop Mode

Loop Mode runs multiple full runs back-to-back on the same goal without the user stepping in between. Each loop is a complete run; each loop reads the memory the previous loops just wrote. The framing that matters: looping isn't about grinding out a better single answer, it's about building up the memory corpus faster. The memory corpus is the product; any one loop's "best painting" is a byproduct.

Trigger phrases: *"loop the monkeys 5 times"*, *"run 10 loops on this"*, *"keep painting"*. The wrapped run mode (Sketch, Standard, Gallery, Deep Troop, Research) determines per-loop sizing.

Detail on cost gating, stopping rules, convergence detection and response (stop / inject chaos / auto-rebranch with mode-aware defaults), the loop session wrapper file, crash recovery, and what the user sees during a session lives in [`references/loop-mode.md`](references/loop-mode.md). Read it whenever Loop Mode is invoked or a loop session continues.

## Memory Reflection

The user can ask the skill to read its own memory and tell them what it has learned. This is reading, not running — no monkeys, no panel, no new run file. It is the memory looking at itself.

### Trigger Phrases

*"What does the memory know about my taste?"*
*"Summarize what we've figured out about whiskey naming."*
*"Which formulations are consistently winning?"*
*"Which failure patterns keep showing up?"*
*"Reflect on the memory."*
*"What has the room learned?"*

When the user asks anything that requires the skill to look backward across runs, route to memory reflection rather than starting a new run.

### Reflection Procedure

1. Read `monkey-memory/indexes/runs.md` (full file or filtered by goal type if the user scoped it).
2. Read `monkey-memory/indexes/formulations.md` and identify formulations with `status: strong` or recurring `supported` evidence.
3. Read `monkey-memory/indexes/failures.md` for recurring patterns.
4. Read `monkey-memory/preferences.md` (project) and, if shared memory is enabled, `~/.monkeys-with-finger-paints/shared/preferences.md`.
5. Read `monkey-memory/entropy.md` for the recent monoculture state.
6. If the user scoped the question (specific goal type, specific lineage, specific formulation family), drill into the relevant individual run files' Agent-Readable Summary sections.

**Stop reading once the user's question is answered.** A scoped question (specific goal type) typically needs only steps 1–4. A broad *"what has the memory learned"* typically needs 1–5. Drilling into individual run files (step 6) is rare — most reflections work from indexes alone.

Synthesize a memory report — what the room currently believes, with cautious language and citations to specific run IDs.

Detail on the synthesis format and citation discipline lives in `references/persistence.md` under "Memory Reflection Procedure".

### Tone

Reflection prose is the same voice as user-facing run output — confident, slightly playful, willing to call out uncertainty. The memory is allowed to be honest about what it doesn't know yet. *"Twelve runs in, the room is convinced you reach for blunt voices. Less convinced about length preferences — the evidence splits. No data yet on long-form prose."*

Do not invent confidence. If a pattern only shows up in 2 runs, say so.

## Final Note

Monkeys with Finger Paints is not a prompt library. It is a skill for building a living evolutionary memory of prompt conditions, outputs, judgments, failures, user taste, and reusable formulations. The purpose is not only to generate better ideas now — it is to make every run teach the next run what kinds of weirdness tend to work for this user, on this kind of goal.

This skill takes itself precisely as seriously as it should and no more. The monkeys send their regards.
