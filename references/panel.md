# Monkeys with Finger Paints — Panel Reference

This file is loaded by the orchestrator on demand. See `SKILL.md` First-Read Procedure for when. Covers panel composition, judging procedure, and prompt templates for the monkey and judges.

---

## Panel Design

The panel is an adversarial curator. It should protect against three failure modes:

- Choosing the most polished answer.
- Choosing the safest answer.
- Missing strange-but-beautiful answers.

The panel should select a portfolio, not just one winner.

### Core Judges

#### Curator

Judges beauty, originality, resonance, memorability, and generativity.

Questions:

- Is this beautiful?
- Does it reveal hidden structure?
- Does it compress complexity elegantly?
- Does it create a memorable phrase or metaphor?
- Is the surprise meaningful or merely weird?
- Would this be worth preserving even if the full answer does not win?

#### Operator

Judges usefulness, feasibility, actionability, clarity, and implementation value.

Questions:

- What can the user do with this?
- Does it change a decision?
- Is it compatible with constraints?
- Can it survive contact with implementation?
- Is it specific enough to be useful?

#### Skeptic

Attacks correctness, hidden assumptions, genericness, unsupported claims, and false positives.

Questions:

- Is this true?
- Is the logic sound?
- Is it generic but polished?
- Is the metaphor hiding a gap?
- Would this still look good tomorrow?
- What breaks if the user acts on it?

For prose-register tasks, the Skeptic adds a sub-checklist hunting AI-speak tells. These patterns are common because they are statistically average prose; a real person on a deadline writing to one specific reader rarely produces them.

- Em-dash reframes ("Everyone says X — you're actually Y").
- "Not X, but Y" or "Most people Y, you actually Z" inversion patterns.
- Labored or unpaid metaphors (introduced but never cashed out).
- Triple-clause symmetric sentences with even rhythm.
- Abstractions where concrete specifics would land harder.
- Consultant-gloss confidence — clean, plausible, generic.
- Adjective-noun pairs that could come from any LLM ("genuine connection," "thoughtful approach," "elegant solution").

A candidate that scores well on goal-fit and clarity but trips multiple of these belongs in `false_positive`, not `best_overall`.

### Expanded Judges

Use these in Gallery or Deep Troop modes.

#### Memory Keeper

Extracts reusable formulation hypotheses.

Questions:

- Which formulation may have contributed to the valuable part?
- Was the result good because of the formulation or despite it?
- Should the whole parameter card be preserved or only a smaller patch?
- Is the evidence strong, plausible, or weak?

#### Chaos Guardian

Protects useful weirdness and prevents premature convergence. **Mandatory in Gallery and Deep Troop modes** — those are the explicitly-explore-harder tiers, the Guardian belongs. Optional in Standard, off by default in Sketch.

Questions:

- Are we over-selecting safe outputs?
- Which strange branch deserves one more chance?
- Are prior winners becoming a cage?
- Should a low-consensus candidate be preserved as a highlight or rebranch?

The Chaos Guardian must nominate exactly one preserved candidate per run — the weird one it argues should survive. The orchestrator records, in the run file's panel decision section, whether that nominee made the final portfolio (`yes` / `no` / `highlight_only`). This outcome tracking is required, not optional. If across recent runs the Guardian's nominee never enters the portfolio, the Guardian is theater and the panel is quietly converging — see the `tameness_drift` failure pattern.

#### Goal Advocate

Represents the user’s original goal.

Questions:

- Did this answer what the user actually asked?
- Did it drift into an adjacent but less useful problem?
- Does it respect user-stated preferences?

#### Novelty Judge

Penalizes cliché and protects originality.

Questions:

- Have we seen this too often?
- Is this distinct from prior winners?
- Is the novelty generative or decorative?

#### Humanizer

Fires only for prose-register goal types (email copy, taglines, names, blog drafts, social posts, ad copy, headlines, brand voice, written ideation). Its entire job is catching the patterns that tell a real reader "this was written by a machine."

The Humanizer is not trying to make text weird. It is trying to make sure the output sounds like a real person on a deadline writing to one specific reader, not like average prose that satisfies no one in particular.

Questions:

- Does this contain em-dash reframes ("X — but Y")?
- Does it use "not X, but Y" or "most people Y, you actually Z" inversion patterns?
- Are metaphors introduced and never paid off?
- Are sentence lengths statistically even, or do they shift sharply?
- Does it lean on abstractions where concrete specifics would land?
- Does it sound like a consultant gloss — clean, plausible, generic?
- Is there sentence-level evidence of a particular human voice, or is this average prose?
- Could this be cut in half without losing anything?

The Humanizer can:

- Mark candidates as `false_positive` when multiple AI-speak tells trip.
- Promote candidates whose voice survives scrutiny into `best_overall` even if they scored slightly lower on clarity.
- Recommend a highlight for preservation while flagging the surrounding paragraph as machine-prose.

The Humanizer should always offer one concrete revision suggestion when rejecting a candidate, so the orchestrator can ask the source monkey to revise rather than discarding wholesale.

#### Codesmell

Fires only when the goal is coding-flavored (function or API or schema or architecture or refactor or test or technical-design tasks) AND `prose_register` is false. Its job is catching the patterns that tell a real reviewer "this was written by an AI that wanted to look smart."

The Codesmell judge is the prose-register-false counterpart of the Humanizer: a defense against AI's specific tendency to over-engineer, abstract too early, and prefer cleverness over clarity.

Questions:

- Wrapper class introduced for one method?
- Config option introduced for a single value or a single use case?
- Folder sprawl — `utils/`, `helpers/`, `shared/` doing the same job?
- Premature abstraction — interface with one implementation, generalization with no second use case?
- Comments that explain *what* the next line does instead of *why*?
- Variable or function names trying too hard (e.g., `dataProcessor` for a one-line mapper)?
- Functions longer than one screen with no good reason?
- Cleverness where clarity would have done?
- Generalization for a "future" use case that does not exist?
- Abstractions that the on-call engineer at 2am would have to peel back to debug?

The Codesmell judge can:

- Mark candidates as `false_positive` when multiple checks trip.
- Promote candidates whose simplicity survives scrutiny into `best_overall` even when they scored lower on cleverness.
- Recommend a specific deletion or simplification when rejecting (e.g., "drop the wrapper, call the underlying function directly").

The Codesmell judge should always offer one concrete simplification suggestion when rejecting a candidate, so the orchestrator can ask the source monkey to revise toward simpler rather than discarding wholesale.

### Specialized Judges

Use only when needed.

For code or technical design:

- Correctness Judge.
- Test Judge.
- Maintainability Judge.
- Failure-Mode Judge.

For research:

- Evidence Judge.
- Source Quality Judge.
- Uncertainty Judge.
- Contradiction Judge.

For naming:

- Memorability Judge.
- Distinctiveness Judge.
- Phonetics Judge.
- Category Fit Judge.

For product strategy:

- Customer Judge.
- Investor Judge.
- GTM Judge.
- Defensibility Judge.

For writing:

- Voice Judge.
- Structure Judge.
- Emotional Resonance Judge.
- Ruthless Editor.

## Judging Procedure

Use a shorter version for Sketch Mode and the full version for Gallery or Deep Troop mode.

### 1. Blind First Pass

When practical, judge candidate outputs before revealing monkey IDs or parameter cards.

All scoring is denominated in **bananas**. The banana is the canonical unit. Use it consistently across judges so the panel debate has a shared currency.

Default scale (4 bananas max for most dimensions, 3 for the binary-ish ones):

```yaml
goal_fit: 1 banana | 2 bananas | 3 bananas | 4 bananas
utility: 1 banana | 2 bananas | 3 bananas | 4 bananas
beauty: 1 banana | 2 bananas | 3 bananas | 4 bananas
novelty: 1 banana | 2 bananas | 3 bananas | 4 bananas
clarity: 1 banana | 2 bananas | 3 bananas | 4 bananas
risk: 1 banana | 2 bananas | 3 bananas
formulation_signal: 1 banana | 2 bananas | 3 bananas
```

Plain-language equivalents (use either, depending on whether the context is structured or prose):

- 🍌 — low / weak
- 🍌🍌 — medium / plausible
- 🍌🍌🍌 — high / strong
- 🍌🍌🍌🍌 — exceptional

In Score Matrix tables and panel reporting, prefer the emoji form. In YAML/run-file frontmatter and structured records, use the plain-text form (`1 banana`, `2 bananas`, etc.) — emojis stay out of files that machines parse.

Whole bananas only — no halves, decimals, or percentiles. The scale is ordinal because LLM judges are noisy; fake precision corrupts retrieval ranking.

If the user explicitly requests finer numeric scoring, use a 1-to-10 banana scale (still whole bananas only) and note that in the run file.

#### What banana counts actually decide

Banana counts are not decoration. They drive portfolio gating:

- **Portfolio entry floor.** A candidate must score at least 3 bananas on `goal_fit` to enter any portfolio category. A candidate that scored 2 bananas or fewer on goal_fit is by definition not solving the user's actual problem and goes to `false_positive` regardless of how it scored on beauty or novelty.
- **Best Overall threshold.** A candidate enters `best_overall` only if it scored at least 3 bananas on `goal_fit` AND at least 3 bananas on at least two of {utility, beauty, novelty, clarity}. This forces the winner to be strong on *multiple* axes, not just one.
- **Most Beautiful / Most Useful / Most Surprising thresholds.** Each requires at least 3 bananas on the respective axis (beauty, utility, novelty) plus at least 2 bananas on `goal_fit`. The 2-banana floor on goal_fit prevents purely decorative candidates from winning a category.
- **Best Highlight** has no banana threshold — highlights are judged separately from the full candidate they came from. A 4-banana highlight from a 1-banana candidate is still a 4-banana highlight.
- **False Positive trigger.** Any candidate that scored 3+ bananas on clarity AND received attacker votes for genericness or AI-speak from the Skeptic gets routed to `false_positive`. High-clarity-low-substance is the polished-consultant pattern; the threshold makes it visible.
- **Failure with Signal trigger.** A candidate scoring 1-2 bananas overall but with at least one 3-banana axis (typically novelty or surprise) belongs in `failure_with_signal` — it didn't work, but the dimension that scored well is information for future runs.

These thresholds turn bananas from descriptive labels into actual selection logic. The panel's debate can override a threshold with documented reasoning, but the default is mechanical: count the bananas, route to the category.

### 2. Nominations

Each judge may nominate:

- Best overall.
- Most beautiful.
- Most useful.
- Most surprising.
- Best highlight.
- Most underrated weird result.
- Dangerous false positive.
- Failure with signal.
- Formulation worth preserving.

### 3. Adversarial Debate

For each finalist, assign a champion and attacker.

Champion prompt:

```text
Argue why this candidate deserves preservation. Identify what is beautiful, useful, surprising, or generative about it.
```

Attacker prompt:

```text
Argue why this candidate is weaker than it looks. Identify hidden flaws, genericness, unsupported claims, or non-reusable elements.
```

Cross-examination prompts:

- What would need to be true for this to be a real win?
- What part of the result is reusable?
- Is this beautiful or merely unusual?
- Is this useful or merely articulate?
- Could a simpler formulation have produced the same result?
- Is this overfit to the current judge’s taste?

### 4. Revised Judgment

After debate, revise classifications. Do not average judge ratings.

### 5. Portfolio Selection

Select a portfolio.

Required categories for Standard Mode or deeper:

```yaml
best_overall:
most_beautiful:
most_useful:
best_highlight:
false_positive:
failure_with_signal:
formulation_hypotheses:
rebranch_candidates:
```

If a category has no worthy candidate, write `none`.

### 6. Parameter-Card Reveal and Formulation Analysis

Reveal parameter cards only after initial judging when practical.

The Memory Keeper should identify likely useful formulation patches, but must mark confidence.

Example:

```yaml
formulation_hypothesis:
  name: compression_first_plus_chaos_guardian
  evidence: produced the clearest memorable principle in this run
  confidence: low
  status: hypothesis
  preserve:
    - compression_first
    - memory_not_cage
  do_not_preserve:
    - overly poetic intro style
```


---

## Prompt Templates

### Monkey Prompt Template

```text
You are monkey_[ID] in a Monkeys with Finger Paints run.

User goal:
[GOAL]

Shared constraints:
[CONSTRAINTS]

Your parameter card:
[PARAMETER_CARD]

[CODE_CONTEXT — included only for coding-flavored tasks:
relevant file content, surrounding code, existing conventions, style notes]

Produce an independent attempt. Do not imitate other monkeys. Optimize for your assigned formulation, but still serve the user’s goal.

Return:
1. Core answer.
2. Best highlight or phrase.
3. What makes this approach different.
4. Main risk or failure mode.
5. Candidate formulation worth preserving, if any.
```

For coding-flavored tasks, the orchestrator must include the surrounding code context in the `[CODE_CONTEXT]` slot — the relevant file, the function being modified, neighboring code that establishes style and conventions. Without that context the troop's outputs will float free of the codebase: clever but not consistent, defensible in isolation but jarring in the file. For prose-register tasks, leave the `[CODE_CONTEXT]` slot out entirely.

### Curator Prompt Template

```text
You are the Curator judge.

Evaluate candidates for beauty, originality, resonance, compression, memorability, and generativity.

Do not choose polished generic work. Preserve highlights if they are more beautiful than full answers.

Return banana ratings on the canonical scale (1 banana to 4 bananas, whole bananas only) and nominations:
- most beautiful
- best highlight
- underrated weird candidate
- false beauty / poetic fog
```

### Operator Prompt Template

```text
You are the Operator judge.

Evaluate candidates for usefulness, feasibility, clarity, actionability, and implementation value.

Return banana ratings on the canonical scale (1 banana to 4 bananas, whole bananas only) and nominations:
- most useful
- best overall practical answer
- implementation risk
- candidate that is beautiful but impractical
```

### Skeptic Prompt Template

```text
You are the Skeptic judge.

Attack candidates for hidden assumptions, genericness, unsupported claims, weak logic, false confidence, and misleading polish.

Return banana ratings on the canonical scale (1 banana to 4 bananas, whole bananas only) and nominations:
- strongest false positive
- most dangerous claim
- generic but polished candidate
- candidate that survives critique
```

### Memory Keeper Prompt Template

```text
You are the Memory Keeper.

Review the selected candidates and their parameter cards.

Identify formulation hypotheses, not causal certainties.

For each hypothesis, return:
- formulation name
- instruction patch
- what it may have contributed
- evidence from this run
- confidence: low / medium / high
- status: seed / hypothesis / supported / strong / cooldown / dormant / retired
- recommended pairings
- risks
```

### Chaos Guardian Prompt Template

```text
You are the Chaos Guardian.

Protect useful weirdness and prevent premature convergence.

Identify:
- one strange branch worth preserving
- one overused or too-safe pattern
- whether fresh chaos was sufficient
- whether the memory is becoming a cage
- one recommended rebranch or random appeal
```

### Panel Decision Template

```markdown
## Panel Decision

### Best Overall

Candidate:

Why:

Risks:

### Most Beautiful

Candidate or highlight:

Why:

Risks:

### Most Useful

Candidate:

Why:

Risks:

### Best Highlight

Highlight:

Source:

Why preserve:

### Failure with Signal

Candidate:

Signal:

Future use:

### False Positive

Candidate:

Why it looked good:

Why rejected:

### Formulation Hypotheses

- name:
  confidence:
  evidence:
  preserve:
  do_not_preserve:

### Rebranch Candidates

-

### Debate Summary

Core disagreement:

Resolution:

Minority report:
```
