# Monkeys with Finger Paints — Quality and Discipline Reference

This file is loaded by the orchestrator on demand. See `SKILL.md` First-Read Procedure for when. Covers formulation lifecycle, entropy memory, causality rules, verification discipline, beauty rules, chaos management, lineage/rebranching, and the failure-pattern catalog.

## Contents

- [Formulation Lifecycle](#formulation-lifecycle) — seed → hypothesis → supported → strong → cooldown → dormant → reactivated/retired
- [Entropy Memory](#entropy-memory) — rolling 10-run window, source/dimension/paint-family monoculture detection
- [Causality Rules](#causality-rules) — cautious formulation-attribution language
- [Verification Discipline](#verification-discipline) — coverage claims require web verification or `unverified` flagging
- [Beauty Rules](#beauty-rules) — banana scale, Skeptic challenges beauty
- [Chaos Management](#chaos-management) — anti-convergence, fresh-chaos floor, cooldown
- [Lineage and Rebranching](#lineage-and-rebranching) — when to track, how to rebranch, crossbreeding sparingly
- [Failure Handling](#failure-handling) — catalog: polished_consultant_convergence, poetic_fog, clever_irrelevance, overfit_memory_echo, unsupported_confidence, unverified_saturation, ai_speak_giveaway

---

## Formulation Lifecycle

Use lightweight lifecycle states.

```text
seed → hypothesis → supported → strong → cooldown → dormant → reactivated or retired
```

### Seed

Provided by the initial library. Not yet proven for this user.

### Hypothesis

Preserved from one or a few runs. Causal evidence is weak.

### Supported

Repeatedly useful across similar goals or confirmed by user preference.

### Strong

Consistently useful for specific goal types or this user.

### Cooldown

Useful but overused. Downweight temporarily to preserve diversity.

### Dormant

Not currently useful but worth preserving for future rebranching.

### Retired

Usually harmful, generic, or superseded. Keep record for negative knowledge.

### Reactivated

Brought back from dormant because a new goal makes it relevant again.

## Entropy Memory

Distinct from cooldown. Cooldown applies to specific named formulations that have been overused — the individual level. Entropy memory tracks distribution at a higher level — recent runs' coverage of formulation *dimensions*, paint families, judge compositions, and goal types. Its job is detecting *uniform monoculture* even when the specific formulations look diverse.

This is the safeguard against memory becoming a cage. Memory should bias future runs without domesticating them.

### What entropy memory tracks

For each rolling window of recent runs (default last 10):

- **Source mix.** What share of monkeys came from seed-or-supported vs nearby variants vs crossbreeds vs fresh chaos. Entropy memory complains if fresh chaos has dropped below 20% across the window, or if any one source has exceeded 50%.
- **Dimension distribution.** Which formulation dimension has carried the unusual vector across recent runs. If five of the last six runs all leaned on Cognitive Strategy as their unusual vector, that's monoculture even if the specific strategies varied.
- **Paint family coverage.** Which named seed formulations have been winning. If `compression_first` has appeared in the winning portfolio of the last four runs, cool it down even if no individual cooldown threshold has tripped.
- **Panel composition.** Which judges have been firing. If the Humanizer has been on every recent run, that may reflect goal-type drift, not preference — flag it.
- **Goal type distribution.** Whether the user is always asking the same kind of question. If 9 of 10 runs are `ideation.naming`, that's not necessarily a problem, but if entropy memory shows it, future runs can deliberately suggest adjacent goal types as rebranches.

### How entropy memory shapes the next run

When a window shows convergence, the next run should:

- Push fresh chaos ratio above the 25% floor toward 35-40%.
- Force the unusual vector onto a dimension that has been underused recently.
- Add a non-default judge (Chaos Guardian, Novelty, or Goal Advocate) whose explicit job is fighting drift.
- Cool down dominant paint families before any individual formulation crosses cooldown.
- Surface a rebranch suggestion from a dormant lineage in the user-facing response.

### Storage

Entropy memory lives at `monkey-memory/entropy.md` as a compact rolling-window table. The current window is summarized; older runs roll off — they remain canonical inside their original run files, the entropy memory just stops counting them.

Example shape:

```markdown
# Entropy Memory (last 10 runs)

## Source mix
| Source | Share | Floor / Ceiling |
|---|---:|---|
| seed_or_supported | 28% | OK |
| nearby_variants | 24% | OK |
| crossbreeds | 19% | OK |
| fresh_chaos | 29% | OK (above 20% floor) |

## Unusual-vector dimension distribution
| Dimension | Count | Note |
|---|---:|---|
| role | 2 |  |
| cognitive_strategy | 5 | OVER-USED — push next run elsewhere |
| style | 1 |  |
| constraint | 1 |  |
| voice_anchor | 1 |  |
| success_metric | 0 | UNDER-USED — force next run here |
| risk_level | 0 |  |

## Paint family heat
| Family | Wins (last 10) | Recommendation |
|---|---:|---|
| compression_first | 4 | cool down |
| skeptical_operator | 3 | watch |
| absurd_metaphor | 1 | OK |
| anti_cliche | 0 | promote candidacy |

## Risk-level distribution
| Risk Level | Count (last 10 runs, summed across monkeys) | Note |
|---|---:|---|
| conservative | 18 |  |
| plausible | 42 |  |
| bold | 14 |  |
| bizarre | 5 |  |
| alien | 1 |  |
| absurd_but_useful | 0 | UNDER-USED — consider forcing one |
**Weirdness floor compliance:** 7/10 runs had ≥1 monkey at risk_level bizarre or higher. Threshold is 100%; failing 3 in 10 means tameness drift may be starting.

## Chaos Guardian outcomes (when fired)
| Run | Guardian's Pick | Made Portfolio? |
|---|---|---|
| run_silent_capuchin | monkey_07 (alien metaphor) | yes |
| run_brave_gibbon | monkey_06 (bizarre constraint) | highlight_only |
| run_curious_mandrill | monkey_08 (absurd voice anchor) | no |

## Recommendations for next run
- Force success_metric or risk_level as unusual vector on at least one monkey.
- Cool down compression_first in the seed pool.
- Weirdness floor: ensure at least one bizarre+ monkey is in the troop.
- Consider adding Chaos Guardian to panel.
- If user is open to it, suggest a rebranch from lineage_L-002 (dormant 4 runs).
```

The principle: cooldown handles *this specific formulation has been overused*. Entropy memory handles *this whole class of behavior has been overused*. Both are needed, because monoculture can hide inside diverse-looking specifics.

### Entropy Memory and Loop Mode

Loop Mode does not get a separate entropy track. Each loop's run contributes to the rolling 10-run window like any other run. Convergence detection in Loop Mode (see SKILL.md) compares the entropy snapshot at loop N against loop N-1 and N-2 — not at session-start vs session-end. The < 10% shift threshold for the source-mix and dimension-distribution rows is the relevant convergence signal.

After each loop completes:

1. The loop's run gets appended to the rolling window; the oldest run rolls off if the window is at 10.
2. Recompute source-mix shares, unusual-vector dimension distribution, paint-family heat, and risk-level distribution.
3. The loop's wrapper file (`monkey-memory/loops/loop_<slug>.md`) records a snapshot of the entropy state at that loop boundary, so the convergence check on the next loop has a concrete N-1 reference point.
4. If the recomputed state shifted < 10% from the prior loop's snapshot AND the other three convergence conditions hold (best-overall same family, no new highlights promoted, no new failure pattern surfaced), increment the consecutive-converged-loops counter. Three in a row triggers convergence.

Convergence in Loop Mode is a *warning*, not a goal. The skill's response is to inject chaos or rebranch, not to silently stop. See SKILL.md → Loop Mode → "What happens on convergence."

## Causality Rules

This skill must be honest about formulation attribution.

An LLM judge cannot reliably know which instruction element caused which output property from one run.

Therefore:

- Treat formulation preservation as hypothesis generation.
- Record confidence.
- Prefer small formulation patches over whole bloated parameter cards.
- Require repeated evidence before marking a formulation supported or strong.
- Track when a formulation may have helped and when it may have hurt.
- Preserve uncertainty.

Use this language:

```text
This formulation may have contributed to...
```

```text
Preserve as a low-confidence hypothesis...
```

```text
This pattern has repeated across several runs and is now supported...
```

Avoid this language unless strongly supported:

```text
This formulation caused...
```

## Verification Discipline

Some claims the skill produces are *coverage claims* — assertions about what already exists in the world. Examples: "this topic is over-covered on podcasts," "this idea has been done to death," "this name is taken," "this story is saturated in true-crime media," "no one has written about this."

The skill cannot reliably make these claims from training-data knowledge alone. Training data ages, lacks coverage of niche corners, and reflects the model's exposure rather than ground truth.

Rules:

- A judge or panel must not classify a candidate as `saturated`, `played out`, `cliche`, `over-covered`, or `unique` based purely on training-data inference. These claims require external verification.
- If web search is available, perform a targeted search before classifying coverage and cite the result in the run file.
- If web search is not available, downgrade the language to "appears familiar to the model's prior knowledge — unverified" and do not treat it as a hard rejection or hard endorsement.
- Coverage suspicion can inform fresh-chaos budgeting (skew toward more fresh chaos when the candidate space feels familiar) without classifying specific candidates as failures.
- The user-facing response must be honest about verification status. Do not present "the model thinks this is over-covered" as if it were verified.
- Track unverified coverage rejections as `unverified_saturation` failures so the pattern is visible across runs.

Use this language:

```text
Appears familiar based on the model's prior knowledge — verification not performed.
```

```text
Web search confirms substantial existing coverage on [platforms]. See [citation].
```

```text
Web search returned minimal prior coverage; treating as low-saturation pending deeper review.
```

Avoid this language unless verified:

```text
This is over-covered.
This is played out.
This is unique.
No one has done this.
```

## Beauty Rules

Beauty is real but noisy.

Do not treat beauty as a precise number by default.

Use the banana scale: 🍌 / 🍌🍌 / 🍌🍌🍌 / 🍌🍌🍌🍌 = low / medium / high / exceptional. Whole bananas only. In structured records, use the plain-text form (`1 banana` ... `4 bananas`); in user-facing prose and Score Matrix tables, prefer the emoji.

A beautiful result may:

- Reveal hidden structure.
- Compress a complex idea elegantly.
- Create a memorable phrase or metaphor.
- Open a new branch of thought.
- Feel inevitable after being seen.
- Be surprising without being arbitrary.
- Improve the user’s framing.
- Generate further useful ideas.

The Skeptic should challenge beauty:

- Is this poetic fog?
- Does the metaphor conceal missing logic?
- Would this still feel good tomorrow?
- Can it survive implementation?

## Chaos Management

The memory should bias future runs but must not eliminate randomness.

Rules:

- Creative runs should always include fresh chaos.
- Do not let prior winners dominate all monkeys.
- Cool down overused formulations.
- Preserve at least one strange branch when it has any plausible signal.
- Track polished generic outputs as false positives.
- Use the Chaos Guardian when convergence risk is high.

Convergence warning signs:

- Winners sound increasingly similar.
- Formulations repeat too often.
- Outputs are clear but unsurprising.
- User expresses boredom.
- Panel keeps selecting polished consultant language.

Responses:

- Increase fresh chaos.
- Add anti_cliche.
- Add absurd_metaphor.
- Rebranch from dormant lineages.
- Use random appeal for one low-scoring weird candidate.
- Cool down dominant formulations.

## Lineage and Rebranching

Lineage is optional in small runs but important over time.

Use lineage tracking when:

- A formulation family recurs across runs.
- A branch repeatedly produces strong outputs.
- A failure pattern keeps appearing.
- The user asks to revisit an older idea.
- A beautiful highlight should be explored later.

### Rebranching

Rebranch from:

- Runner-ups.
- Beautiful highlights.
- Failures with signal.
- Dormant lineages.
- User-favored outputs.
- Overlooked weird candidates.

Rebranch prompt:

```text
Return to [candidate/highlight/formulation]. Keep what was promising, change what failed, and explore 3-5 new descendants with fresh formulations.
```

### Crossbreeding

Crossbreeding combines two or more formulation hypotheses.

Example:

```yaml
crossbreed:
  parent_1: compression_first
  parent_2: skeptical_operator
  parent_3: absurd_metaphor
  goal: produce memorable but implementation-aware product positioning
```

Use crossbreeding sparingly. Too many combined formulations create attribution noise.

## Failure Handling

Failures are first-class artifacts.

Preserve failures when they reveal:

- A bad formulation.
- A misleadingly polished style.
- A recurring trap.
- A useful negative constraint.
- A branch that should become dormant.
- A weird idea that failed but might work elsewhere.

Common failure patterns:

### polished_consultant_convergence

The output is clear, plausible, and generic.

Causes:

- Too many safe strategy formulations.
- Judges overweight clarity.
- Low chaos.
- No anti-cliche judge.

Avoidance:

- Add anti_cliche.
- Add Curator or Novelty Judge.
- Preserve strange highlights separately.

### poetic_fog

The output feels beautiful but lacks meaning or actionability.

Causes:

- Poetic style without Skeptic or Operator.
- Metaphor not cashed out.

Avoidance:

- Pair poetic_compression with skeptical_operator.
- Require concrete implications.

### clever_irrelevance

The output is surprising but does not serve the goal.

Causes:

- Too much inversion or absurd metaphor.
- Weak Goal Advocate.

Avoidance:

- Add Goal Advocate.
- Require explicit connection to user goal.

### overfit_memory_echo

The output repeats prior winners without real novelty.

Causes:

- Too little fresh chaos.
- Dominant formulation not cooled down.

Avoidance:

- Increase fresh chaos.
- Cool down repeated formulations.
- Rebranch from dormant nodes.

### unsupported_confidence

The output sounds authoritative but lacks evidence.

Causes:

- Research or factual task without evidence judge.
- Beauty or novelty overweighted.

Avoidance:

- Add Evidence Judge.
- Require citations or uncertainty.

### unverified_saturation

A candidate is rejected or labeled "over-covered," "played out," or "cliche" based on the model's training-data impression rather than verified external evidence.

Causes:

- Coverage claims treated as facts without web verification.
- Judge uses "I think this has been done a lot" as a hard rejection criterion.
- Skill confidently dismisses candidates the model happens to associate with familiar territory.

Avoidance:

- Apply the Verification Discipline rules: coverage claims require web search or explicit `unverified` flagging.
- Downgrade unverified saturation suspicion to a fresh-chaos input, not a candidate rejection.
- When in doubt, present the candidate to the user with the verification status flagged, and let the user decide.

### ai_speak_giveaway

Output reads as obviously machine-generated despite being technically competent. The candidate satisfies goal-fit and clarity but trips multiple AI-speak tells: em-dash reframes, "not X but Y" inversions, labored or unpaid metaphors, statistically even sentence rhythms, abstractions where concrete specifics would land harder, consultant-gloss confidence without lived detail, generic adjective-noun pairs.

This is a prose-register failure mode. It does not apply to code, technical specs, structured research, or planning output where consultant register can be the right register.

Causes:

- Monkey parameter cards did not include a voice anchor or anti-AI-speak constraint formulation.
- Panel did not include the Humanizer judge for a prose-register task.
- Skeptic did not run its prose-task sub-checklist.
- Goal type was not properly tagged as prose register.

Avoidance:

- Tag prose-register tasks correctly at goal interpretation (Step 1).
- Add the Humanizer judge to the panel for those tasks.
- Pair voice-anchor formulations with prose paints (`compression_first`, `anti_cliche`, `poetic_compression`, etc.).
- Run the Skeptic's prose-task sub-checklist.
- Ground monkeys in a specific human reference — a writer, a publication, a known piece of writing, or a stated voice ("write like a senior PM venting on Slack").

Recovery: when the panel detects this mid-run, ask the source monkey to revise with a stronger voice anchor and a "cut every sentence that could come from any LLM" pass before reselection.

### wrapper_class_compulsion

A coding-task winner introduced a wrapper class around a single function call, an interface with one implementation, or a class that exists only to hold one method. The Codesmell judge missed it or didn't fire.

Causes: panel weighted "looks designed" over "is simple." Codesmell judge wasn't in the panel. Monkey parameter card pulled toward enterprise-Java aesthetics on a task that didn't call for them.

Avoidance: include the Codesmell judge for coding-flavored tasks. Add a constraint formulation like "no class unless at least two callers exist." Pair `implementation_architect` with `ruthless_editor` so the design pass is followed by a deletion pass.

### config_flag_accretion

A coding-task winner introduced a config option, feature flag, or runtime parameter for a single use case or value. Configuration is now load-bearing for nothing.

Causes: monkey reached for "make it configurable" as a default move. No constraint formulation enforcing "pick one." Codesmell judge didn't fire.

Avoidance: add a constraint formulation like "no new config option unless a second caller needs a different value." The Codesmell judge should explicitly check for this.

### premature_abstraction

A coding-task winner generalized for a second use case that doesn't exist yet. Interface, type, or function is shaped around imagined future callers rather than the one real caller.

Causes: monkey optimized for "extensible" without evidence the second case is coming. Skeptic didn't challenge the speculative future.

Avoidance: constraint formulation: "design for the one caller you have; the second caller can refactor when they show up." Codesmell judge flags single-use abstractions.

### folder_sprawl

A coding-task winner created or kept multiple folders doing the same job — `utils/` and `helpers/` and `shared/`, or `lib/` and `common/` and `core/`. Where things go is now unclear.

Causes: monkey didn't read the existing repo's conventions, or there were no conventions and the monkey invented three. Codesmell judge didn't fire.

Avoidance: include code context in the monkey prompt so existing conventions are visible. Codesmell judge flags folder duplication. Constraint formulation: "if the same kind of thing already lives somewhere, put it there."

### cleverness_over_clarity

A coding-task winner chose a clever construction (a one-liner, a metaprogramming trick, a too-pretty abstraction) over a simple, plainly-readable version. The on-call engineer at 2am will hate it.

Causes: monkey optimized for elegance or compression as a virtue in itself. Codesmell judge didn't apply the on-call test.

Avoidance: add the "would the on-call engineer at 2am cry?" check to the Codesmell judge. Voice anchor: "the kind of code that won't make the on-call engineer cry at 2am."

### comment_telling_what

A coding-task winner added comments that restate what the next line of code does instead of explaining why it's there or what invariant it depends on.

Causes: monkey treated comments as a coverage metric. No constraint formulation distinguishing what-comments from why-comments.

Avoidance: constraint formulation: "comments explain why or what's invariant, never what; if a comment restates the code, delete it." Codesmell judge flags what-comments.

### tameness_drift

Across recent runs, the troop's outputs have been getting safer. Fewer than 15% of winners came from monkeys with risk_level bizarre or higher; or the Chaos Guardian's preserved candidate has not entered a portfolio in 5+ runs; or the panel keeps quietly demoting weird candidates in favor of polished ones.

This is the audit-time failure mode. It catches the slow drift that more discipline (more judges, more failure patterns, more rules) inevitably pulls toward. Each new rule is one more force pulling toward safety.

Causes: weirdness floor not enforced (no monkey per run with risk_level bizarre+). Chaos Guardian not present in Gallery/Deep Troop. Fresh chaos ratio drifted below 25%. Memory-derived monkeys outweighed fresh chaos consistently. Panel composition leaned conservative (no Chaos Guardian, no Novelty Judge).

Avoidance:

- Enforce the weirdness floor: at least one monkey per Standard+ run carries risk_level bizarre or higher.
- Make Chaos Guardian mandatory in Gallery and Deep Troop (it is, per panel reference).
- Track Chaos Guardian outcome (yes/no/highlight_only) in every run that fires it.
- Track risk-level distribution per run in entropy memory.
- When entropy memory shows the pattern, raise fresh chaos ratio toward 35-40% on the next run, force the unusual vector onto risk_level for at least one monkey, and consider giving Chaos Guardian veto power on portfolio composition for that run.

Recovery: when detected, the next run is run with the Chaos Guardian explicitly empowered to overrule the panel on one portfolio entry. Document the recovery in the run file.
