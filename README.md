<img width="1920" height="819" alt="monkeys-with-fingerpaint-banner" src="https://github.com/user-attachments/assets/52b2852c-ea55-44bb-8fb1-67f5aa1d5254" />

# 🐵🍌 MONKEYS WITH FINGER PAINTS 🍌🐵

*Reckless on purpose. Sharper over time.*

AI is bland by default. Trained on the average, it produces the average. Ten flavors of one idea, no matter how you prompt. Other tools work around this with templates, chains, and clever framings. They don't fix it.

Monkeys with Finger Paints fixes it permanently. It works on any problem: naming, strategy, debugging, refactoring, framing, copy, hiring, even how to phrase a hard email. Every problem benefits from extremely creative divergent thinking. The skill surfaces angles nothing else does.

Picture a troop of monkeys with finger paint. Each one gets the same goal, a different temperament, a different jar of paint, and different constraints. Most produce garbage. That is the design. Being absurd, deviating from the mean, experimenting wildly is how you find the answer single-model brainstorming could never reach.

A panel walks in afterward, throws away the failures, keeps the surprises, and writes down the chain of custody: which monkey produced what, which paint led to which result, and which results you kept. Stick around and offer feedback, or opt to allow the loop to run autonomously.

Either way, each idea is traced back to the inputs that made it, building a knowledge base of how to reproduce "good."

The next batch of monkeys reads those notes. The batch after that has more notes still. The memory handles multiple topics simultaneously. Each problem domain builds its own thread, and cross-domain patterns of your taste emerge. The troop gets sharper every time you run it.

Loop it overnight while you sleep. Ask the memory what it has learned about your taste; it will tell you. The blandness does not come back. 🐵🍌

## MEET THE TROOP

- **`PITCH.md`**: why this skill exists. The monkey manifesto.
- **`SKILL.md`**: the operating spec. Read this to invoke the skill or understand the mechanism.
- **`monkey-memory/README.md`**: a primer for anyone landing in a skill's memory output without context.

## THE LAYOUT

```
SKILL.md            # runtime procedure and principles
PITCH.md            # why-this-exists pitch
LICENSE             # Apache 2.0
NOTICE              # attribution requirement (preserve in derivatives)
references/         # deep-dive content the skill loads on demand
  formulations.md   # dimensions, seed library, voice anchors, fresh chaos
  panel.md          # judges, judging procedure, prompt templates
  persistence.md    # memory model, source-of-truth, templates, retrieval, consolidation
  execution.md      # execution modes, runtime adapters, spawn rules, simulated-mode constraints
  response-style.md # canonical run-output template, voice, panel reporting, meme beats, monkey display names
  loop-mode.md      # loop invocation, cost gating, convergence detection and response, wrapper file
  quality.md        # lifecycle, entropy, causality, verification, beauty, chaos, lineage, failures
templates/          # file templates the skill uses (run, formulation, lineage, panel decision)
monkey-memory/      # persistent memory (populated by runs, gitignored)
```

## WHEN TO RELEASE THE MONKEYS

Strong fit for tasks with multiple defensible answers where taste matters: naming, positioning, framing, voice, copy, refactor strategies, API design, postmortem framings. The skill won't refuse simpler questions, but the memory and panel might be overkill on a single-answer lookup.

Modes you can invoke by name:

- **Loop Mode:** multiple back-to-back runs on the same goal that compound the memory corpus.
- **Memory Reflection:** ask the skill to read its own memory and report what it's learned about your taste, without running new monkeys.
- **User Judge Mode:** say *"I'll judge"* and the panel still scores but you pick the winners, with explicit criteria and the troop's full output surfaced.
- **Sketch / Gallery / Deep Troop:** scale from 4 monkeys (fast) to 48 (deep) by saying the mode name.

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
> *The Curator went hard for Rita, kept calling it "the only real painting." The Skeptic torched Bonzo for the third-clause sentence. Operator was bored. Panel landed on Rita.*
>
> *[the full output: What the monkeys made, What the panel said, The portfolio, Next moves, Memory updates]*
>
> *🐵🐵🐵🐵🐵🐵🐵🐵 SHOW'S OVER 🐵🐵🐵🐵🐵🐵🐵🐵*

(Monkey emojis flanking each title equal the troop size on each side: eight for Standard mode, four for Sketch, sixteen-plus for Gallery.)

## THE FINE PRINT

Apache 2.0. See `LICENSE` for the full text and `NOTICE` for the attribution requirement on derivatives.

## CHIEF MONKEY

Nicholas Tiedemann
