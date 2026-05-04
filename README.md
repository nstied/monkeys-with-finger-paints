# Monkeys with Finger Paints

A Claude skill for chaos-preserving evolutionary ideation. Spawns many parallel mutated subagents (monkeys) on a goal, has an adversarial panel pick a portfolio of winners, beautiful highlights, and reusable formulation hypotheses, and preserves results in a Markdown memory that biases future runs.

The brainstorm is the medium. The memory corpus is the product.

## Where to Start

- **`PITCH.md`** — why this skill exists, written from the monkeys' perspective. Read first if you're deciding whether to use it.
- **`SKILL.md`** — operating spec. Read if you're invoking the skill or want to understand what it actually does.
- **`monkey-memory/README.md`** — orientation for anyone walking into the memory a skill produces, without having read the spec.

## Layout

```
SKILL.md           — runtime procedure and principles
PITCH.md           — why-this-exists pitch
LICENSE            — MIT
references/        — deep-dive content the skill loads on demand
  formulations.md  — dimensions, seed library, voice anchors, fresh chaos
  panel.md         — judges, judging procedure, prompt templates
  persistence.md   — memory model, source-of-truth, templates, retrieval, consolidation
  execution.md     — execution modes, runtime adapters, spawn rules, simulated-mode constraints
  response-style.md — canonical run-output template, voice, panel reporting, monkey display names
  loop-mode.md     — loop invocation, cost gating, convergence detection and response, wrapper file
  quality.md       — lifecycle, entropy, causality, verification, beauty, chaos, lineage, failures
templates/         — file templates the skill uses (run, formulation, lineage, panel decision)
monkey-memory/            — persistent memory (populated by runs)
```

## Use For

Creative ideation with multiple defensible answers — naming, positioning, framing, voice, copy, refactor strategies, API design, postmortem framings, and similar work where novelty and beauty matter as much as correctness.

Beyond a single run, the skill also supports:

- **Loop Mode** — multiple back-to-back runs on the same goal that compound the memory corpus.
- **Memory Reflection** — ask the skill to read its own memory and report what it's learned about your taste, without running new monkeys.
- **User Judge Mode** — say *"I'll judge"* and the panel still scores but you pick the winners, with explicit criteria and the room's full output surfaced.
- **Sketch / Gallery / Deep Troop** — scale from 4 monkeys (fast) to 48 (deep) by saying the mode name.

## Best For

Tasks with multiple defensible answers and where taste matters. The skill won't refuse simpler questions, but the memory and panel overhead aren't earning their keep on a single-answer lookup.

## License

MIT. See `LICENSE`.

## Author

Nicholas Tiedemann.
