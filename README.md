<img width="1920" height="819" alt="monkeys-with-fingerpaint-banner" src="https://github.com/user-attachments/assets/52b2852c-ea55-44bb-8fb1-67f5aa1d5254" />

# WELCOME TO MONKEY TOWN

Sometimes random monkeys do great things.

This skill uses random monkeys to fight AI blandness with directed chaos. Give it a goal and it spawns a bunch of monkeys (subagents) with varying personalities. Each monkey independently works toward the goal. When all the monkeys are done, an adversarial panel judges their outputs, designates highlights and components worth keeping, and preserves the cause-effect chain in a Markdown memory that biases future runs.

Beware. The monkey troop gets smarter and smarter over time. Eventually, it might just become smarter than you.

The brainstorm is the medium. The memory corpus is the product.

## MEET THE TROOP

- **`PITCH.md`** — why this skill exists. The monkey manifesto.
- **`SKILL.md`** — operating spec. Read if you're invoking the skill or want to understand what it actually does.
- **`monkey-memory/README.md`** — orientation for anyone walking into the memory a skill produces, without having read the spec.

## THE LAYOUT

```
SKILL.md           — runtime procedure and principles
PITCH.md           — why-this-exists pitch
LICENSE            — Apache 2.0
NOTICE             — attribution requirement (preserve in derivatives)
references/        — deep-dive content the skill loads on demand
  formulations.md  — dimensions, seed library, voice anchors, fresh chaos
  panel.md         — judges, judging procedure, prompt templates
  persistence.md   — memory model, source-of-truth, templates, retrieval, consolidation
  execution.md     — execution modes, runtime adapters, spawn rules, simulated-mode constraints
  response-style.md — canonical run-output template, voice, panel reporting, meme beats, monkey display names
  loop-mode.md     — loop invocation, cost gating, convergence detection and response, wrapper file
  quality.md       — lifecycle, entropy, causality, verification, beauty, chaos, lineage, failures
templates/         — file templates the skill uses (run, formulation, lineage, panel decision)
monkey-memory/     — persistent memory (populated by runs, gitignored)
```

## WHEN TO RELEASE THE MONKEYS

This skill enhances AI instances by encouraging broad, diverse thinking. It eliminates bland, boring, averaged-to-the-mean reasoning that often plagues AI systems. It remembers what "good" is. It handles changing topics within a single chat / instance / repo and develops a sense of taste over time.

Strong fit for creative ideation with multiple defensible answers — naming, positioning, framing, voice, copy, refactor strategies, API design, postmortem framings, and similar work where novelty and beauty matter as much as correctness.

Beyond a single run, the skill supports:

- **Loop Mode** — multiple back-to-back runs on the same goal that compound the memory corpus.
- **Memory Reflection** — ask the skill to read its own memory and report what it's learned about your taste, without running new monkeys.
- **User Judge Mode** — say *"I'll judge"* and the panel still scores but you pick the winners, with explicit criteria and the troop's full output surfaced.
- **Sketch / Gallery / Deep Troop** — scale from 4 monkeys (fast) to 48 (deep) by saying the mode name.

## WHEN BANANAS ARE EARNED

Tasks with multiple defensible answers where taste matters. The skill won't refuse simpler questions, but the memory and panel might be overkill on a single-answer lookup.

## A GLIMPSE

What a run feels like, abbreviated:

> *🐵🐵🐵🐵🐵🐵🐵🐵 WELCOME TO MONKEY TOWN 🐵🐵🐵🐵🐵🐵🐵🐵*
>
> *Recruiting Monkey 01 / Pickles (failure-mode-first), Monkey 02 / Rita (patio11-voiced), Monkey 03 / Mojo (alien metaphor)... Painting now.*
>
> *(time passes)*
>
> *🙉 BANANAS IN THE AIR*
>
> *(more time)*
>
> *🐒 PANEL IN SESSION*
>
> *(panel scores, debates)*
>
> *✨ THE PANEL HAS SPOKEN*
>
> *The Curator went hard for Rita — kept calling it "the only real painting." The Skeptic torched Bonzo for the third-clause sentence. Operator was bored. Panel landed on Rita.*
>
> *[the full output: What the monkeys made, What the panel said, The portfolio, Next moves, Memory updates]*
>
> *🐵🐵🐵🐵🐵🐵🐵🐵 SHOW'S OVER 🐵🐵🐵🐵🐵🐵🐵🐵*

(The monkey emojis flanking each title equal the troop size on each side — eight for Standard mode, four for Sketch, sixteen-plus for Gallery. You can see the scale of the run at a glance.)

## THE FINE PRINT

Apache 2.0. See `LICENSE` for the full text and `NOTICE` for the attribution requirement on derivatives.

## CHIEF MONKEY

Nicholas Tiedemann
