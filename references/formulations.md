# Monkeys with Finger Paints — Formulations Reference

This file is loaded by the orchestrator on demand. See `SKILL.md` First-Read Procedure for when. Covers formulation generation: parameter dimensions, seed library, voice anchors, and fresh chaos generation.

## Contents

- [Formulation Generation](#formulation-generation) — how to compose parameter cards
- [Formulation Dimensions](#formulation-dimensions) — Role, Cognitive Strategy, Style, Risk Level, Constraint, Success Metric, Voice Anchor
- [Voice Anchor sub-library](#voice-anchor) — writers, fictional characters, stated personas, brand voices, plus invention guidance
- [Seed Formulation Library](#seed-formulation-library) — ~20 named seeds with instruction patches
- [Fresh Chaos Generation](#fresh-chaos-generation) — four-tier procedure (web → memory-negation → deterministic → self-distance)

---

## Formulation Generation

Use formulations deliberately. Avoid producing many monkeys that differ only cosmetically.

A formulation is a reusable pattern across the parameter card handed to a monkey. The card has three buckets:

- **Monkey personality parameters:** role, stance, risk appetite, audience assumption, and success metric. This is who the monkey is while painting.
- **Finger Paint parameters:** cognitive strategy, constraints, domain lens, evidence requirements, fresh-chaos seed, source material, and failure mode to avoid. This is what the monkey has in its hands.
- **Painting-style parameters:** style, voice anchor, format, length, structure, and register. This is what the finished painting should look and sound like.

Good runs vary all three buckets across the troop. A run with eight different painting styles but the same monkey personality and same finger paint is not diverse.

### Formulation Dimensions

Each dimension below ships with seed examples to prevent blank-page paralysis. The seeds are starters, not menus. Inventing a value on the fly — specific, slightly weird, named after a real reference or scenario — almost always outperforms picking a seed verbatim. When in doubt, invent.

#### Role — monkey personality parameter

Examples:

- Contrarian strategist.
- Product philosopher.
- Ruthless operator.
- Skeptical investor.
- Customer advocate.
- Naming poet.
- Systems architect.
- Adversarial researcher.
- Anti-cliche editor.
- Lineage cartographer.
- Evidence judge.
- Failure-mode critic.

Inventing roles is encouraged. "An internal critic from Google's Brand Council in 2014" is a sharper role than "anti-cliche editor" because it carries unstated assumptions about taste, scrutiny, and historical moment. "Your eccentric friend who reads too much Hacker News and has shipped twice" is sharper than "skeptical investor" because it has a vibe.

#### Cognitive Strategy — finger paint parameter

Examples:

- First principles.
- Analogy.
- Inversion.
- Constraint exaggeration.
- Compression first.
- Customer-backward reasoning.
- Failure-mode-first reasoning.
- Hypothesis troop.
- Contradiction hunting.
- Recombination.
- Red-team critique.

Inventing strategies is encouraged. "Argue from the assumption the user is dead wrong about the framing" is more generative than "inversion." "Build the answer from the most expensive failure mode backward" is sharper than "failure-mode-first." Concrete and slightly weird beats canonical and clean.

#### Style — painting-style parameter

Examples:

- Terse memo.
- Founder memo.
- Technical spec.
- Poetic highlight.
- Brutalist checklist.
- Academic abstract.
- Field notes.
- Investor pitch.
- Product manifesto.

Inventing styles is encouraged. "A Stratechery memo with HN top comments at the bottom" is a sharper style than "founder memo." "Patagonia field notes meets a Reddit AMA" is a sharper style than "field notes." Style and Voice Anchor often blur into each other on prose-register tasks; that is fine.

#### Risk Level — monkey personality parameter

Risk Level is the one categorical dimension that does not invite invention — it is meant as a coarse dial, not a place for cleverness.

Examples:

- Conservative.
- Plausible.
- Bold.
- Bizarre.
- Alien.
- Absurd but useful.

#### Constraint — finger paint parameter

Examples:

- Explain the idea in one sentence first.
- Assume no demo is possible.
- Optimize for memorability.
- Make it implementable tomorrow.
- Avoid common category language.
- Use only concrete examples.
- Start from the strongest objection.
- Force one strange metaphor.
- Produce three mutually incompatible answers.
- Make the idea work under half the time and half the budget.

Inventing constraints is encouraged. "Every sentence must contain a number or a proper noun." "No word longer than two syllables." "One paragraph, no more — and the first sentence must be a question." Constraints are the cheapest dimension to invent and often the most productive — they force concrete moves the monkey would not have made on its own.

#### Success Metric — monkey personality parameter

Examples:

- Beauty.
- Usefulness.
- Correctness.
- Novelty.
- Strategic leverage.
- Implementation readiness.
- Memorability.
- Reusability.
- Emotional resonance.

Inventing metrics is encouraged. "The friend test — would your most skeptical friend forward this?" is sharper than "memorability." "The print test — would this hold up printed and pinned to a wall?" is sharper than "beauty." A specific imagined audience or situation produces tighter writing than an abstract metric.

#### Voice Anchor — painting-style parameter

Apply only on prose-register tasks. A voice anchor grounds the monkey in a specific human reference — a writer, a publication, a fictional character, a stated persona — instead of letting it default to statistical-mean prose.

The instruction is "write in the spirit of X," not "produce a pastiche of X." The anchor orients the prose. It does not constrain it to imitation.

The lists below are a starter, not a menu. Invented anchors usually outperform seed examples because they fit the specific task. See "Invented anchors" below.

**Writers, journalists, essayists:**

- Ben Thompson / Stratechery (analytical-but-conversational; specific examples; long but rhythmically varied).
- Patrick McKenzie / patio11 (specific, slightly impatient, technical but human).
- Paul Graham (declarative, concrete examples, contrarian without being cute).
- Helen Rosner (sensory specificity; surprising images that earn their place).
- Jia Tolentino (accessible literary criticism; inhabits ambivalence).
- Joan Didion essay voice (observational, distanced, sentence-level precision).
- Matt Levine / Money Stuff (chatty, parenthetical, knowing about finance).
- Casey Newton / Platformer (clear, accessible analysis without breathless takes).
- Anthony Bourdain in Kitchen Confidential (kinetic, blunt, sensory, profane when it earns it).
- A Defector article (knowing voice; conversational expertise; small jokes that don't try too hard).
- A McSweeney's column (deadpan absurdist confidence; structurally tight).
- A Kevin Kelly post (compressed wisdom; very short, very sharp).
- A Stewart Brand Whole Earth blurb (compressed, product-focused, specific).

**Fictional characters with distinctive voices:**

- Sheldon Cooper from The Big Bang Theory (literal, condescending, technically precise).
- Don Draper from Mad Men (declarative, mid-century masculine, weighted pauses).
- Selina Meyer from Veep (profane, exhausted, transactional).
- Larry David in Curb Your Enthusiasm (petty grievance escalating into principle).
- Tyrion Lannister from Game of Thrones (ironic, literary, drunk-philosopher).
- Sherlock Holmes BBC (clipped, condescending, deductive).
- Lorelai Gilmore from Gilmore Girls (rapid pop-culture references, manic warmth).
- Detective Pembleton from Homicide (interrogator's logic, righteous).
- House MD (caustic, bored, brilliant).
- Hermione Granger (precise, slightly impatient, citing sources).

**Stated personas and scenarios:**

- "A senior PM venting on Slack to one trusted peer."
- "A tweet from someone who has shipped this exact thing."
- "An internal Slack message at 11pm before launch."
- "Your most direct mentor with ten minutes between meetings."
- "A coworker after their second beer telling you the real story."
- "A friend at a competitor texting you off-the-record."
- "A bartender giving advice during a slow shift."

**Brand and copy voices:**

- Old Spice ad copy (escalating absurdist confidence).
- Apple early Mac launch copy (declarative, clean, one claim per beat).
- Liquid Death water can copy (heavy metal energy applied to water).
- Cards Against Humanity copy (deadpan transgressive).

**Coding voices** (for prose-register-false coding tasks — naming, API design, refactor strategy, PR descriptions, doc strings, postmortems):

- The Linux kernel commit message style (brutal clarity, no fluff, one sentence imperative subject).
- Patrick McKenzie / patio11 writing a Python library (specific, slightly impatient, technical without being dry).
- Jane Street OCaml docstrings (precise, dense, assumes a smart reader).
- The SQLite documentation (declarative, durable, deeply explained when it matters).
- Stripe API docs (clean, example-first, escape hatches noted).
- "A senior reviewer leaving a thoughtful but firm PR comment" (direct, specific, not preachy).
- "A postmortem from someone who actually fixed it" (no narrating, just what broke and what changed).
- Holzmann / JPL safety-critical code voice (short functions, explicit assertions, no surprises, every line earns its place).
- "The kind of code that won't make the on-call engineer cry at 2am" (invented persona — readable under stress).
- "A junior dev's question being answered for the third time, kindly" (patient, exact, no condescension).

**Invented anchors.** The orchestrator should invent voice anchors on the fly when the goal calls for a register the lists don't cover. Specific and slightly weird almost always outperforms generic. Examples:

- "Write like Sheldon from The Big Bang Theory if he were briefly being kind."
- "Write like a Twitter thread from someone who once worked at this company and is amused but not bitter."
- "Write like the quiet older sibling at Thanksgiving who finally says one true thing."
- "Write like a senior infrastructure engineer answering a junior dev's question for the third time, but kindly."
- "Write like the kind of LinkedIn post that gets shared because it's actually useful, not because it's emotional."
- "Write like a long-time forum user who has the actual answer and has typed this answer many times before."

A named, specific, slightly unusual invented anchor will outperform a generic seed almost every time. When in doubt, invent.

Risk: imitation rather than orientation. Monkeys producing pastiche instead of voice. Mitigated by the Skeptic's prose checklist and the Humanizer judge.

### Seed Formulation Library

Use these as initial formulation families when no memory exists.

#### compression_first

Use for positioning, naming, strategy summaries, taglines, and conceptual clarity.

Instruction patch:

```text
Before expanding, compress the answer into one sentence, one phrase, and one design principle.
```

Risks: May discard nuance too early.

#### skeptical_operator

Use for product strategy, implementation, plans, and feasibility.

Instruction patch:

```text
Treat the idea as something that must survive implementation. Identify what is useful, what breaks, and what must be made concrete.
```

Risks: May suppress beauty or weirdness.

#### absurd_metaphor

Use for naming, creative strategy, writing, and conceptual reframing.

Instruction patch:

```text
Force an unusual metaphor from an unrelated domain, then extract only the parts that create useful insight.
```

Risks: Can become arbitrary or decorative.

#### anti_cliche

Use for writing, positioning, naming, and strategy.

Instruction patch:

```text
Reject polished generic language. Identify and replace familiar category phrases with more specific, alive language.
```

Risks: Can overcorrect into strangeness.

#### customer_backwards

Use for product, UX, GTM, and positioning.

Instruction patch:

```text
Start from the customer’s pain, confusion, desire, or objection. Build the answer backward from that lived experience.
```

Risks: Can narrow the frame too much.

#### founder_memo

Use for strategy, vision, principles, and product direction.

Instruction patch:

```text
Write as a sharp founder memo: opinionated, compressed, strategic, and oriented toward action.
```

Risks: Can become overconfident.

#### implementation_architect

Use for technical design, systems, workflows, and operational plans.

Instruction patch:

```text
Turn the idea into components, interfaces, data flow, failure modes, and first implementation steps.
```

Risks: Can flatten the creative spark.

#### failure_mode_first

Use for critique, planning, technical design, strategy, and risk analysis.

Instruction patch:

```text
Start by identifying how this fails. Then design the answer to survive those failures.
```

Risks: Can become overly negative.

#### hypothesis_troop

Use for research, diagnosis, strategy, and ambiguous problems.

Instruction patch:

```text
Generate multiple competing hypotheses before choosing a direction. Preserve uncertainty until the evidence or reasoning justifies narrowing.
```

Risks: Can delay synthesis.

#### contradiction_hunter

Use for research, argument, critique, and synthesis.

Instruction patch:

```text
Look for tensions, contradictions, and unresolved tradeoffs. Use them as the source of insight.
```

Risks: Can overemphasize conflict.

#### poetic_compression

Use for naming, writing, brand, strategy language, and beauty selection.

Instruction patch:

```text
Favor language that compresses complexity into memorable, resonant phrases without becoming vague.
```

Risks: Can produce poetic fog.

#### ruthless_editor

Use for writing, specs, summaries, and clarity.

Instruction patch:

```text
Cut anything generic, redundant, decorative, or weak. Preserve only what sharpens the result.
```

Risks: Can remove useful nuance.

#### lineage_cartographer

Use for memory, rebranching, system design, and iterative projects.

Instruction patch:

```text
Track where ideas came from, which branches they open, which branches they close, and where future rebranching should happen.
```

Risks: Can add overhead.

#### memory_not_cage

Use for long-running creative systems and prompt evolution.

Instruction patch:

```text
Use prior winners as bias, not law. Preserve novelty and creativity. Explicitly identify where history may overconstrain the answer.
```

Risks: Can reduce exploitation of known good patterns.

#### chaos_guardian

Use for creative tasks and evolutionary runs.

Instruction patch:

```text
Protect at least one weird, underexplored, or low-consensus branch from being eliminated too early.
```

Risks: Can preserve noise.

#### naming_phonetics

Use for naming and taglines.

Instruction patch:

```text
Judge names by sound, memorability, distinctiveness, and ease of saying aloud.
```

Risks: Does not handle legal clearance.

#### investor_skeptic

Use for business models, strategy, and pitches.

Instruction patch:

```text
Interrogate leverage, defensibility, market pull, adoption friction, and why this should exist now.
```

Risks: Can over-index on venture logic.

#### retrieval_first

Use for memory, knowledge systems, and context-window design.

Instruction patch:

```text
Design the answer so future agents can retrieve the right information quickly without reading everything.
```

Risks: Can over-structure early work.

#### constraint_exaggerator

Use for creativity, product design, strategy, and planning.

Instruction patch:

```text
Take one real constraint and exaggerate it. Use the exaggerated version to reveal a sharper solution.
```

Risks: Can distort priorities.

#### inversionist

Use for strategy, critique, problem-solving, and ideation.

Instruction patch:

```text
Invert the obvious goal, assumption, or success metric. Use the inversion to discover non-obvious moves.
```

Risks: Can become clever but irrelevant.

### Fresh Chaos Generation

Fresh chaos is only as fresh as its source. A monkey labeled "fresh chaos" but composed entirely of the orchestrator's imagined weirdness will produce correlated outputs over many runs — the model has its own default ideas about what "weird" looks like, and those defaults converge into a recognizable signature. The skill produces chaos autonomously, never soliciting creative input from the user. Use whichever of the tiers below is available, in priority order.

#### Tier 1 — Web entropy (preferred when web search is available)

Pull a fresh external seed before composing the parameter card:

- A top headline from a news aggregator (Hacker News, a major newspaper homepage, an industry feed).
- A random Wikipedia article (`en.wikipedia.org/wiki/Special:Random`).
- A trending sports result, weather event, stock mover, or local news item.
- The title of a blog post or paper published in the last 48 hours.
- A high-rated comment from a recent forum thread.

Encode the seed as a constraint formulation on the chaos monkey's parameter card. The default constraint is: *"your output must include at least two distinct words or phrases drawn from this seed."* This forces real integration rather than gestural reference.

Record the seed in the parameter card:

```yaml
entropy_seed:
  tier: 1
  source: "ycombinator.com/news"
  date: "2026-05-02"
  text: "Open-source large language model leak prompts new licensing debate"
  constraint: "include at least two phrases from this headline"
```

The seed is preserved in the run file so future agents can audit whether the chaos was external.

#### Tier 2 — Memory-negation (preferred fallback when web is unavailable)

Memory accumulates patterns the panel and the model have come to favor. Even across different goal types, those patterns share inherited biases — same kinds of metaphors, same structural moves, same "how a winning answer feels." Drawing from memory, even cross-domain, recombines accumulated success rather than producing divergence.

Memory-negation flips the logic: the chaos monkey is composed by *forbidding* what memory has been favoring. The goal is guaranteed divergence from accumulated bias.

Procedure:

- Pull the active forbidden-set from `monkey-memory/entropy.md` (or compute on the fly from recent winners): every formulation name, role, style, constraint, voice anchor, and phrase that has appeared in a winning portfolio across the last 10 runs.
- Compose the chaos parameter card from values that are *not* in the forbidden set. Invent values where the seed library is exhausted by negation. The orchestrator must demonstrate at least one dimension where it picked something memory has actively been avoiding or never tried.
- Add an explicit anti-convergence constraint to the chaos monkey: *"do not produce output that resembles the recent winning portfolios. Specifically, avoid these phrases, structures, and moves: [list from forbidden-set]."*

Record as:

```yaml
entropy_seed:
  tier: 2
  source: "memory-negation"
  forbidden_set_size: 23
  excluded_examples: ["compression_first", "skeptical_operator", "em-dash reframes", "founder_memo"]
  novel_choices: ["role: a tarot reader who used to do user research", "constraint: every line must end in a question"]
  anti_convergence_constraint: "do not resemble recent winners"
```

This tier requires non-trivial memory (at least 5 runs with portfolios). Below that, fall through to Tier 3.

#### Tier 3 — Deterministic seed from goal text and date

When neither web nor cross-domain memory entropy is available:

- Hash the goal text concatenated with today's ISO date and the current run number.
- Use the hash to pick a forced pairing of two seed formulations the orchestrator would not naturally combine for the current goal type (e.g., for a naming task, force `failure_mode_first` + `poetic_compression`).
- Add an inverted-assumption constraint derived from the goal itself: *"assume the user wants the opposite of what they asked for; find what's true about that inversion and bring it back."*

Record as `entropy_seed: { tier: 3, seed: <hash>, forced_pairing: [...], inversion: "..." }`. This is more deterministic than truly external, but at least the choice isn't filtered through the model's "what feels weird" reflex.

#### Tier 4 — Model self-distance (last resort)

When no external, memory, or deterministic source applies, fall back to a structured self-distancing procedure:

- Enumerate five first-instinct chaos parameter cards for the task.
- Discard the most "obvious" two as model-default weirdness (the ones that feel comfortable to write).
- Pick the remaining parameter card that feels most uncomfortable.
- Apply anti-default constraints to the chosen parameter card: no em-dashes, no seed formulations from the active library, no canonical roles, no metaphors from the model's most-overused domains (biology, jazz, chess, military strategy, mountaineering).
- Record as `tier: 4`.

Tier 4 should be flagged in the run file. If entropy memory shows recent chaos has been mostly Tier 4, the orchestrator should record this as a quality concern in the run file's retrieval capsule, so future runs know the chaos history was thin. The orchestrator does not push the user to take action — chaos quality is the system's problem, not the user's homework.

#### User-volunteered seeds (accepted, never solicited)

If the user happens to volunteer a word, phrase, object, color, dice roll, or unrelated topic at invocation time ("run monkeys on this — and use the word 'avalanche' somewhere"), incorporate it as if it were Tier 1 entropy and record `tier: 1, source: "user"`. Do not ask the user for a seed. Do not put friction in front of an invocation. The skill's job is to produce chaos on its own; user contribution is a bonus, never a requirement.

#### Skeptic check on chaos integration

The Skeptic adds one verification step for any monkey that received an entropy seed: *did the output integrate the seed, or merely gesture at it?* A chaos monkey that received "include two phrases from today's NYT homepage" but produced output where those phrases are decorative (a parenthetical mention, a footnote) gets demoted to `false_positive`. The seed must do real work in the output.

#### Why this matters

The four tiers attack two convergence forces: the model's training-data middle (Tiers 1 and 3) and memory's accumulated success patterns (Tier 2). Tier 4 is the last-resort backstop.

Memory-negation is not crossbreeding. Crossbreeding (under Lineage and Rebranching) recombines past winners; negation forbids them. Both have a place — different ends.
