---
schema_version: 1
run_id: run_<adjective>_<primate>      # ALWAYS include the "run_" prefix. e.g. run_silent_capuchin (matches filename run_silent_capuchin.md)
date: YYYY-MM-DD
goal: ""
goal_type: ""
run_mode: standard
monkeys: 8
judges:
  - Curator
  - Operator
  - Skeptic
fresh_chaos_ratio: 0.25
execution_mode: native_parallel_subagents
runtime_adapter: other
formulation_schema_version: 2
coverage_status: not_applicable
produced_under_skill_version: 0.5.8
memory_used: true
# loop_session and loop_position: include both when this run is part of a Loop Mode session, omit both otherwise.
# loop_session: loop_<adjective>_<primate>      # e.g. loop_silent_capuchin
# loop_position: 3        # 1-indexed within the session, e.g. 3 of 5
status: complete
tags: []
---

# Run run_<adjective>_<primate>: [Short Goal]

<!--
Section legend:
  REQUIRED — every run file has this section.
  CONDITIONAL — include only when the trigger condition fires (noted at the top of each).
  Don't add freelance H2 sections beyond what's listed here. If you have something extra to say, weave it into an existing section.
-->

## Agent-Readable Summary
<!-- REQUIRED. Quick scan for future agents. One short paragraph or a few labeled lines. -->

Goal:

Best overall:

Most beautiful:

Most useful:

Best highlight:

Failure with signal:

False positive:

Formulation hypotheses:

User preference signals:

Recommended future branches:

## Score Matrix
<!-- REQUIRED. One row per candidate (or per pitch when monkeys produced multiple pitches). Banana scores per judge plus total. -->

| Candidate | Topic | Curator | Operator | Skeptic | Humanizer | Σ |
|---|---|---:|---:|---:|---:|---:|
| monkey_01_pitch_1 | [topic] | 🍌🍌🍌 | 🍌🍌🍌🍌 | 🍌🍌🍌🍌 | 🍌🍌🍌🍌 | 14 |

<!--
Multi-pitch naming: when a monkey produces multiple candidate outputs in one run, identify them as `monkey_NN_pitch_M`
(e.g. monkey_01_pitch_1, monkey_01_pitch_2). Never compress to `m1p1` or similar — the no-compression rule from the
canonical-ID format applies here too.
-->

## Panel Decision
<!-- REQUIRED. The portfolio. Use the categories that actually fired in this run; omit ones that didn't. -->

- **Best Overall:**
- **Most Useful:**
- **Most Beautiful:**
- **Most Surprising:**
- **Best Highlight:**
- **False Positive(s):**
- **Failure with Signal:**

## Monkey Parameter Cards
<!-- REQUIRED. Single-block YAML for every monkey in the troop. Critical for cross-run formulation traceability and memory consolidation. -->

```yaml
monkey_01:
  unusual_vector:
  source:
  monkey_personality:
    role:
    stance:
    risk_appetite:
    audience_assumption:
    success_metric:
  finger paint:
    cognitive_strategy:
    constraints:
      -
    domain_lens:
    evidence_requirements:
    fresh_chaos_seed:
    source_material:
    failure_mode_to_avoid:
  painting_style:
    style:
    voice_anchor:
    format:
    length:
    structure:
    register:
```

## Best Highlights
<!-- REQUIRED. Quoted text worth preserving. Each line: the highlight in quotes, attribution to monkey_NN / Display Name. -->

- *"[highlight]"* — monkey_NN / [Display Name]

## Formulation Hypotheses
<!-- REQUIRED. Patterns observed this run. Mark confidence and any promotion. -->

| Pattern | Origin | Confidence | Status |
|---|---|---|---|

## Convergence Analysis
<!-- CONDITIONAL. Include when run is part of a Loop Mode session OR when convergence (or its absence) was a meaningful signal this run. Otherwise omit. -->

## NEW Failure Patterns Logged
<!-- CONDITIONAL. Include only when a new failure pattern surfaced this run. Reference by technical name; cross-link to references/quality.md if appropriate. Otherwise omit. -->

## Lineage Updates
<!-- CONDITIONAL. Include when a lineage threshold was crossed or when this run extends an existing lineage. Otherwise omit. -->

## User Judgment
<!-- CONDITIONAL. Include when the user judged, overrode the panel, or critiqued the output post-run. Distill the critique to a sentence or two; don't paste verbatim. Otherwise omit. -->

## Retrieval Capsule
<!-- REQUIRED. Forward-looking guidance for future runs that retrieve this one. -->

Use this run when:
-

Do not use this run when:
-

Best reusable formulation hypotheses:
-

User preference signals:
-

Failure patterns:
-

Rebranch candidates:
-

Key phrases or highlights:
-

## Selected Raw Outputs
<!-- CONDITIONAL. Include only when raw monkey outputs are being preserved (per the Raw Attempts policy in references/persistence.md). Otherwise omit. -->
