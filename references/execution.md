# Monkeys with Finger Paints — Subagent Execution Reference

This file is loaded by the orchestrator on demand. Read at Steps 5–7 of the High-Level Workflow (running monkeys, prepping candidates, panel judging) or whenever execution mode / runtime adapter is in question. Covers execution modes, runtime adapters, how monkeys and judges are spawned, cost and latency, simulated-execution constraints, and orchestrator responsibilities. The high-level principle ("monkeys work alone") lives in `SKILL.md`; everything below is detail.

---

## Execution Modes

```yaml
execution_modes:
  native_parallel_subagents:
    status: preferred
    meaning: independent workers run concurrently
  native_serial_subagents:
    status: acceptable_degraded
    meaning: independent workers run one after another
  simulated_single_context:
    status: last_resort
    meaning: the orchestrator generates attempts itself with scratchpad files
    restrictions:
      - record execution_mode: simulated_single_context
      - do not promote formulation confidence above low from that run alone
      - treat diversity evidence as weaker than subagent-produced diversity
```

## Runtime Adapters

Use the host runtime's closest independent-worker primitive:

- **Claude Code (verified):** Task calls with the `general-purpose` subagent type.
- **Codex (designed for, untested):** available agent delegation/spawn primitives when the user invocation authorizes agent work; otherwise simulated mode.
- **Antigravity / other runtimes (designed for, untested):** whatever tool creates isolated worker contexts. If none exists, `simulated_single_context`.

Only Claude Code has been exercised against this spec at the time of writing. Codex and Antigravity portability is a forward-looking design intent, not a tested guarantee. If you run this skill under a non-Claude-Code runtime and it works, great; if it doesn't, the spec is wrong about that runtime — file the gap.

Do not write runtime-specific APIs into run records as if they are universal. Record the logical mode (`native_parallel_subagents`, `native_serial_subagents`, or `simulated_single_context`) and, if useful, the runtime adapter used.

## How Monkeys Are Spawned

In `native_parallel_subagents`, send all monkey calls in one batch when the runtime allows it, so they execute concurrently. In `native_serial_subagents`, launch the same independent prompts sequentially. In either native mode, the orchestrator does not generate monkey outputs itself.

Each monkey's prompt contains:

- The user's original goal (full text).
- Shared constraints.
- The monkey's parameter card (compact YAML).
- Output requirements: core answer, best highlight, what makes this approach different, main risk, candidate formulation worth preserving.

## How Judges Work

Blind first-pass scoring should also use independent workers when available. Each judge role (Curator, Operator, Skeptic, etc.) receives candidate summaries with monkey IDs and parameter cards hidden.

Judges run in parallel when possible. Their outputs are then combined by the orchestrator (the main skill context) for nominations and adversarial debate.

Adversarial debate (champion/attacker per finalist) can be either:

- Two more worker calls per finalist (one champion, one attacker), or
- Performed by the orchestrator synthesizing across the blind-pass outputs.

For Sketch and Standard mode, orchestrator-side debate is acceptable. For Gallery and Deep Troop, prefer separate worker calls for champion and attacker so their arguments are genuinely independent.

## Cost and Latency Implications

A Standard run (8 monkeys + 3 judges) is ~11 worker calls. A Gallery run with debates is ~27. Batch in parallel; serial defeats the point. If parallel isn't available, fall back to serial workers before falling back to single-context simulation.

## Simulated Execution Has Real Constraints

When simulation is the actual execution mode, the orchestrator generates monkey outputs in its own context, and panel scoring + debate + synthesis layered on top overflow a single context window. Per-monkey scratchpad files become required, not optional.

Under `execution_mode: simulated_single_context`:

- Write each monkey's output to `monkey-memory/runs/run_<slug>_raw/monkey_NN.md` as generated.
- Clear monkey output from active context once written.
- Re-read individual monkey files only as the panel needs them (candidate summaries during nominations, specific finalists during debate).
- The `_raw/` directory is governed by the Raw Attempts policy — deep memory, not user-facing. Top-level `outputs/` stays sacred regardless.

Under native execution modes, no scratchpad is needed; worker results are the storage mechanism.

## Orchestrator Responsibilities

The orchestrator plans the troop, spawns workers (monkeys + judges + optional champion/attacker), collects outputs, synthesizes the panel decision, and writes the run file and indexes. In native modes it does not generate monkey outputs itself; in simulated mode it may, under the constraints above. Cross-monkey reasoning always happens in the orchestrator.
