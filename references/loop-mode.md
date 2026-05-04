# Monkeys with Finger Paints — Loop Mode Reference

This file is loaded by the orchestrator on demand, only when the user invokes Loop Mode (or when continuing an existing loop session). Covers invocation, cost gating, stopping rules, convergence detection and response, loop memory writes, crash recovery, and what the user sees during a session. The trigger phrases and a one-paragraph principle live in `SKILL.md`; everything below is the runtime detail.

---

Loop Mode runs multiple full runs back-to-back on the same goal without the user stepping in between. Each loop is a complete run — monkeys, panel, memory writes — and each loop reads the memory the previous loops just wrote.

**Why it exists.** Looping is not about grinding out a slightly better single answer. It is about building up the memory corpus faster. A single run leaves a small footprint in memory; ten loops leave a meaningful one. The memory corpus is the product. The "best painting" from any one loop is a byproduct.

This framing matters when reporting: at the end of a loop session, lead with what the room learned, not with which run had the highest banana score.

## Invoking Loop Mode

The user invokes by saying things like *"loop the monkeys 5 times"*, *"run 10 loops on this"*, or *"keep painting"*. Loop Mode wraps any of the existing run modes — Sketch, Standard, Gallery, Deep Troop, Research — and the wrapped mode determines per-loop sizing.

```yaml
loop_session:
  loop_count: 5            # explicit count, OR
  loop_until: convergence  # auto-detect saturation and ask the user how to respond
  per_loop_mode: standard  # the run mode each loop uses
  cost_cap: 200_worker_calls   # default ceiling; triggers mid-session confirmation
  goal: <user goal>
```

If the user gives just a number, default per-loop mode to Standard. If the user gives a mode but no count, default to 3 loops. If neither, default to 3 Standard loops.

**Cost gating.** Loop counts ≤ 10 run without confirmation. Counts 11–25 surface estimated worker calls and require a "go" before starting. Counts 26+ require explicit Deep-Troop-style confirmation. The default `cost_cap: 200_worker_calls` is a soft ceiling — when crossed mid-session, the orchestrator pauses and asks whether to continue.

## Stopping Rules

Any session can be interrupted by the user; the rules below describe automatic stops.

1. The configured `loop_count` is reached.
2. The `cost_cap` is hit.
3. **Convergence detected** (defined below).

**Convergence — what it means in this skill.** Convergence is not a goal here; it is a warning. The point of Monkeys with Finger Paints is to *avoid* converging on the most fluent average answer. When the room starts producing the same painting on repeat, the right move is rarely "stop and accept the average" — it is to throw more chaos or rebranch.

**Convergence fires when ALL FOUR conditions hold across THREE consecutive loops:**

- (a) best-overall came from the same formulation family,
- (b) zero new highlights promoted,
- (c) zero new failure patterns surfaced,
- (d) entropy memory's source-mix and dimension-distribution shifted < 10% from the prior loop's snapshot.

All-four-and-three-loops is intentionally conservative. False-positive saturation hurts more than running one extra loop.

**What happens on convergence.** The orchestrator does not silently stop. It surfaces the saturation in plain language — *"🙈 The room's tired. Three loops in a row producing the same painting. Want to inject chaos, rebranch, or stop?"* — and offers three responses, with a per-mode default:

- **(A) Stop** — accept the memory state and wrap the session.
- **(B) Inject chaos** — bump `fresh_chaos_ratio` to 50%, force at least 2 monkeys to `risk_level: alien` or higher, and force a Chaos Guardian into the panel for the remaining loops.
- **(C) Auto-rebranch** — pick the most promising dormant lineage or unexplored branch from memory, switch the goal context, and resume looping there.

Default action by mode:

```yaml
convergence_default_action:
  sketch: B   # inject chaos and finish the loop count
  standard: B
  gallery: C  # rebranch — Gallery already has a Chaos Guardian; just changing the room
  deep_troop: C
  research: A # research wants stability, not more chaos
```

The user can override the default at any time. The session log records which response was taken so the room learns what the user reaches for when it saturates.

## Loop Memory Writes

Each loop writes its own run file (`monkey-memory/runs/run_<slug>.md`) under the same persistence rules as a single run. Loop sessions also write a session-level wrapper file, **created at session start with `loops_completed: 0` and `stop_reason: in_progress`, then incremented after each member loop completes**.

```yaml
# monkey-memory/loops/loop_<slug>.md frontmatter
schema_version: 1
loop_id: loop_<adjective>_<primate>      # e.g. loop_silent_capuchin
date: YYYY-MM-DD
goal: ""
goal_type: ""
per_loop_mode: standard
loops_attempted: 5
loops_completed: 5
stop_reason: in_progress | loop_count_reached | cost_cap | convergence_stopped | convergence_chaos_injected | convergence_rebranched | user_stopped | orphaned
member_runs:
  - run_silent_capuchin
  - run_brave_gibbon
  - run_curious_mandrill
  - run_jolly_tamarin
  - run_swift_macaque
```

The loop file's body summarizes what changed across the session: which formulations strengthened, which failures recurred, which highlights earned promotion, whether convergence was detected and how the user responded. It is a session retrospective, not a re-paste of the member runs.

**Crash recovery.** If a wrapper file is left in `stop_reason: in_progress` for more than 24 hours, the next skill invocation auto-finalizes it to `stop_reason: orphaned` and notes the gap in the session retrospective. The original member runs remain canonical.

## What the User Sees

During the session: a one-line stage-setting note before each loop, and a brief atmospheric line between loops. *"Loop 3 of 5. Bananas in the air."* No long synthesis between loops — that's what the loop wrapper is for.

At session end: a corpus-first summary. *"Five loops. The room learned X, strengthened Y, surfaced new failure pattern Z. Best single painting was from loop 3. Here are the rebranch suggestions for next time."* The wrapper file path goes in the response so the user can browse the full session record.
