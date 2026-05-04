# Monkeys with Finger Paints — Persistence Reference

This file is loaded by the orchestrator on demand. See `SKILL.md` First-Read Procedure for when. Covers the persistence model, source-of-truth hierarchy, promotion thresholds, file templates, retrieval procedure, and memory consolidation.

---

## Persistence Model

Markdown is the source of truth.

**Memory lives in the user's working directory by default, not in the skill install directory.** Earlier versions of this skill accidentally wrote memory inside `.claude/skills/...` (or whatever the host runtime's skill cache path was). That was a bug — it tied a project's accumulated memory to the install location and lost it whenever the skill was reinstalled or moved. v0.3.0 fixes this.

Per-project layout (the default):

```text
<user-working-directory>/
  monkey-memory/
    .config.yaml         # memory location config, sticky after first answer
    runs/
      run_silent_capuchin.md
      run_brave_gibbon.md
    loops/               # only present if Loop Mode has been used
      loop_silent_capuchin.md
    formulations/
      compression_first.md
      skeptical_operator.md
    lineages/
      lineage_0001.md
    indexes/
      runs.md
      formulations.md
      goals.md
      failures.md
      highlights.md
    preferences.md       # project-local preferences (HOW + WHAT)
    entropy.md
    README.md
```

Shared memory layout (only when the user opts in, per project):

```text
~/.monkeys-with-finger-paints/shared/
  preferences.md         # promoted HOW-preferences only
  .meta.yaml             # cap, decay-window, last-consolidation tracking
```

`<user-working-directory>` is whatever folder the skill was invoked from — the project the user is working in. The skill never writes memory inside its own install path.

If the environment cannot write files, produce a compact run record in the response so the user can save it.

### Memory Location and First-Run Setup

The first time the skill runs in a new working directory:

1. **Detect existing memory.** If `monkey-memory/.config.yaml` exists, skip setup — answers are already recorded.
2. **Bootstrap the per-project `monkey-memory/` folder** (runs/, indexes/, etc.) per the Minimal First Run procedure in SKILL.md.
3. **Detect shared memory.** Check whether `~/.monkeys-with-finger-paints/shared/` exists.
4. **Ask the user, exactly once per project:**
   > *"Memory will live inside this project by default. Do you also want to share preferences across all your projects? (y/n)"*
   - On `y`, set `shared_memory_enabled: true` and bootstrap the shared path if it doesn't exist (just create the folder, an empty `preferences.md` with the template, and `.meta.yaml`).
   - On `n`, set `shared_memory_enabled: false`. Nothing about this project ever leaves it.
5. **Write `monkey-memory/.config.yaml`** with the answer. Sticky — never asked again for this project.

The `.config.yaml` file:

```yaml
schema_version: 1
project_memory_path: monkey-memory/
shared_memory_enabled: true | false
shared_memory_path: ~/.monkeys-with-finger-paints/shared/
oriented: true            # set after first-run orientation; suppresses re-orientation
created: YYYY-MM-DD
```

The user can change their mind by editing `.config.yaml` directly. Flipping `shared_memory_enabled` from `false` to `true` does not retroactively promote past observations — promotion still requires the 2-project threshold going forward.

### Shared Preferences — What Crosses, What Doesn't

This is the cross-contamination guard. Naive sharing dumps everything into one file and applies whiskey-naming taste to a coding decision (or vice versa). The discipline that prevents this:

**HOW-preferences vs WHAT-preferences.**

| Type | Definition | Eligible for sharing? | Examples |
|---|---|---|---|
| **HOW** | Stylistic, cognitive, or register preferences that hold across domains. | Yes, after promotion threshold. | "Reaches for blunt voices, not consultant voices." "Kills outputs that sound like AI-speak." "Prefers concrete specificity over abstract framing." "Consistently overrides 'polished' winners in favor of weirder runners-up." "Likes adversarial critics." |
| **WHAT** | Anything tied to a specific domain, vocabulary, technology, or subject matter — *including* engineering-taste-in-code (which spans multiple coding projects but doesn't apply to whiskey naming). | No — stays per-project always. | "Liked outlaw-country names for the whiskey project." "Prefers Postgres over DynamoDB for OLTP workloads." "Hates wrapper classes." "Wants the monkeys skill's brand voice to be irreverent." |

Classification rule: HOW = how the user wants to think and read. WHAT = anything tethered to a domain, technology, or subject. **When in doubt, classify as WHAT.** Conservative classification is the entire point of the guard.

Edge cases — worked through:

- *"Likes adversarial critics"* → **HOW**. About how the user wants ideas tested. Applies to whiskey naming, code review, strategy, anything.
- *"Wants the skill itself to be theatrical"* → **WHAT**. About this specific project — the skill — not the user's general taste.
- *"Hates wrapper classes"* → **WHAT**. Engineering-specific. Doesn't apply to non-code work even though it spans multiple coding projects. (Earlier draft of this skill incorrectly classified this as HOW; that was wrong.)

**Promotion threshold.** A HOW-preference does not move to shared the first time it is observed. It must be observed in **at least 2 different projects** before promotion. Until then it lives in the project's own `preferences.md` with a counter.

**Mid-project flip behavior.** When the user changes `shared_memory_enabled` from `false` to `true` partway through a project's life, only HOW signals observed *after* the flip count toward the 2-project threshold. The skill does not retroactively scan past runs.

**Promotion procedure at end-of-run:**

1. For each new HOW-preference signal observed this run, write or update the entry in the project's `preferences.md` with `cross_project_observation_count: 1`.
2. If shared memory is enabled, check the cross-project registry (see Match Algorithm below) for the same preference observed in another project.
3. If found in 2+ projects (including this one), promote: write to shared `preferences.md` with `confidence: low, last_reinforced: YYYY-MM-DD, cross_project_count: N`.
4. The project copies remain — they're the evidence trail.

### Match Algorithm

Two preferences "match" for promotion purposes via this procedure (run by the orchestrator at end-of-run, not by a panel judge):

1. **Normalize** each preference text — lowercase, strip punctuation, lemmatize (treat "voices/voice", "writing/wrote/writes" as the same root).
2. **Compute Jaccard overlap** on the normalized token sets: `intersection / union`.
3. **Decision band:**
   - Overlap ≥ 0.6 → **same preference**, auto-merge.
   - Overlap < 0.4 → **distinct preferences**, do not merge.
   - 0.4 ≤ overlap < 0.6 → **ambiguous**, the orchestrator may make one short LLM call: *"Are these the same user preference, yes or no?"* — single-token answer is fine.

This is deliberately simple. It is not embedding-similarity. The cost of a wrong merge (collapsing two genuinely different preferences) is higher than the cost of a missed merge (the duplicate eventually gets caught at consolidation).

### Cross-Project Registry

The shared memory directory keeps a registry that lets the skill recognize the same project across runs without depending on absolute paths.

```yaml
# ~/.monkeys-with-finger-paints/shared/.meta.yaml
schema_version: 1
shared_memory_path: ~/.monkeys-with-finger-paints/shared/
total_runs_observed: 0
runs_since_last_consolidation: 0
last_consolidation_date: null
known_projects:
  - project_hash: 8a3f...           # sha256 of git remote URL if present, else canonical abs path
    display_name: "whiskey-naming"  # human-readable, last-known
    last_known_path: /Users/nstied/Documents/whiskey-naming
    last_seen: YYYY-MM-DD
  - project_hash: c12e...
    display_name: "monkeys-with-finger-paints"
    last_known_path: /Users/nstied/Documents/monkeys-with-finger-paints
    last_seen: YYYY-MM-DD
```

The hash makes the registry portable across machines and resilient to renames. If the git remote URL is available, it's the canonical input to the hash; otherwise the canonical absolute path is. If a registered project's path is unreachable (deleted, moved without git remote, on a disconnected drive), the registry treats it as "skip, don't fail" — its preferences don't count toward this run's promotion check, but it stays in the registry for next time.

**Cap, decay, consolidation.**

```yaml
shared_preferences_constraints:
  max_entries: 50
  decay:
    window_runs: 10
    rule: "if a preference is not reinforced (re-observed or re-applied without override) within the last 10 runs across all projects, decrement confidence by one tier"
  eviction:
    trigger: file_at_cap
    rule: "evict lowest-confidence entries first; tiebreak by oldest last_reinforced date"
  consolidation:
    triggers:
      - file_at_cap
      - runs_since_last_consolidation: 20
    procedure: "the orchestrator reads the shared file, merges near-duplicates per the Match Algorithm above ('reaches for blunt voices' + 'prefers plain language' → one entry with the higher confidence), recomputes confidence, evicts as needed"
```

**Write failure behavior.** Shared writes can fail — permissions, missing dir, readonly FS, full disk. The skill does not silently swallow these. On a failed shared write, the orchestrator surfaces the error to the user and asks: retry now, skip just this entry, or disable shared memory for the rest of the session. The run itself remains complete; the failure is logged in the run file's `Notes` section as `shared_write_failed: <error>`.

**Override demotes.** When the user overrides a panel call influenced by a shared preference, log the override against that preference and decrement its confidence one tier. If confidence reaches zero, evict immediately. Override is the strongest signal in the system — stronger than repeated observation.

### Persistence by Mode

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

Sketch Mode is allowed to be ephemeral. If a Sketch run produces a valuable highlight, user preference, or reusable failure, write a compact run note and update only the relevant indexes. Standard Mode and deeper should always persist when file access exists.

### Execution and Formulation Schema Metadata

Every run file should identify how it was executed and which formulation schema it uses.

```yaml
execution_mode: native_parallel_subagents | native_serial_subagents | simulated_single_context
runtime_adapter: claude_task | codex_spawn_agent | other | none
formulation_schema_version: 2
coverage_status: verified | mixed | unverified | not_applicable
```

Formulation schema versions:

```yaml
formulation_schema_versions:
  1:
    status: legacy
    fields: [paints, source, approach]
    interpretation: infer dimensions only as low-confidence metadata
  2:
    status: current
    fields:
      - unusual_vector
      - monkey_personality:
          - role
          - stance
          - risk_appetite
          - audience_assumption
          - success_metric
      - finger paint:
          - cognitive_strategy
          - constraints
          - domain_lens
          - evidence_requirements
          - fresh_chaos_seed
          - source_material
          - failure_mode_to_avoid
      - painting_style:
          - style
          - voice_anchor
          - format
          - length
          - structure
          - register
```

When reading legacy v1 runs, the Memory Keeper may infer v2 dimensions for retrieval convenience, but must not use inferred dimensions for formulation promotion, entropy counts, or high-confidence attribution unless later evidence confirms them.

Coverage status:

- `verified`: coverage/saturation claims cite external search or sources.
- `mixed`: some coverage claims are verified and some are explicitly marked unverified.
- `unverified`: coverage/saturation claims are model-prior impressions only.
- `not_applicable`: the run makes no coverage claims.

**When the orchestrator sets coverage_status to non-default.** A run is making a coverage claim whenever a panel judge or the synthesis layer asserts that something is *over-covered*, *played out*, *cliché*, *unique*, *novel-in-the-world*, *first-of-its-kind*, *already done*, or *underexplored*. These are claims about what exists in external work — not claims about what the troop produced. Detection rule for the orchestrator at end-of-run: scan the panel decision and final synthesis for any of those phrases (or close paraphrases). If found, the run made coverage claims and `coverage_status` must be set to `verified`, `mixed`, or `unverified` based on whether web search backed those claims.

If the panel never asserts coverage about external work — typical for a pure ideation, naming, or technical-design task where the brief is "give me options" rather than "give me something nobody else has done" — `coverage_status` stays `not_applicable` and that's the right answer.

Future retrieval must treat unverified coverage claims as weak signals only. They may influence fresh-chaos budgeting, but they must not hard-reject a candidate.

### Source-of-Truth Hierarchy

Markdown is the source of truth, but not all Markdown has the same authority. The memory distinguishes between event records, entity records, and navigation indexes.

```yaml
runs/*.md:
  canonical_for:
    - what the user asked
    - troop configuration
    - monkey parameter cards used in that run
    - candidate summaries
    - panel decision
    - selected outputs and highlights from that run
    - user judgment during that run
    - formulation hypotheses first observed in that run
    - failures and false positives from that run
  not_canonical_for:
    - long-term formulation status after later evidence
    - global highlight library
    - cross-run user preference profile
    - lineage state after later runs

formulations/*.md:
  canonical_for:
    - promoted formulation hypotheses
    - current formulation status
    - confidence level
    - accumulated evidence across runs
    - recommended pairings and anti-pairings
    - cooldown or retirement state
  created_only_when:
    - a formulation crosses the promotion threshold (see below)

lineages/*.md:
  canonical_for:
    - promoted branch history
    - recurring ancestry and rebranch notes
    - dormant or dead-end branch status
  created_only_when:
    - a branch recurs or is explicitly preserved for rebranching

preferences.md:
  canonical_for:
    - user-visible preference memory
    - explicit user taste notes
    - low-confidence inferred preferences

indexes/*.md:
  canonical_for:
    - nothing except navigation convenience
  role:
    - derived pointers
    - compact summaries
    - links or references to canonical files
```

When information appears in more than one place, prefer the more specific canonical file:

- *What happened in run_0007?* → `runs/run_0007.md`
- *What is the current status of compression_first?* → `formulations/compression_first.md` if it exists; otherwise the run files and indexes serve as evidence.
- *Which runs mention beautiful highlights?* → `indexes/highlights.md`, then open the canonical run file.
- *What does the system currently believe about the user's taste?* → `preferences.md`.

Indexes must never become independent truth stores. They point to canonical records, not replace them.

### Promotion Thresholds

Most observations live only inside the run file. Entity files (formulation, lineage, highlight) are created only when an observation earns promotion.

**Formulation promotion.** A formulation hypothesis gets its own `monkey-memory/formulations/[name].md` file when at least one of these is true:

1. It is selected by the panel as a formulation hypothesis in 2 separate runs.
2. The user explicitly says to preserve it.
3. It is selected as best formulation in a Gallery or Deep Troop run.
4. It is reused in a later run and appears useful again.
5. Important enough as a failure pattern or anti-pattern to track globally.

Before promotion, formulation hypotheses live inside the originating run file and may appear in `indexes/formulations.md` as unpromoted pointers. Newly promoted formulations start at `status: hypothesis, confidence: low`. Only repeated evidence moves them to `supported`, `strong`, or `cooldown`.

**Highlight promotion.** Highlights normally remain canonical inside the run file where they appeared. A highlight gets promoted to a global highlight index entry only when at least one of these is true:

1. The user explicitly marks it as worth preserving.
2. The panel selects it as Best Highlight.
3. It becomes a reusable phrase, metaphor, naming candidate, or design principle.
4. It is re-used or rebranched in a later run.
5. It is needed as a cross-run reference for a lineage or formulation.

Even after promotion, the originating run file remains canonical for the highlight's origin and context. The global highlight entry is a pointer plus short description.

**Lineage promotion.** Create or update `monkey-memory/lineages/[name].md` only when at least one of these is true:

1. A branch appears across 2 or more runs.
2. The user asks to rebranch from a prior result.
3. A formulation family develops a meaningful ancestry.
4. A dead end or dormant branch is important enough to avoid or revisit.
5. A Gallery or Deep Troop run explicitly selects a rebranch candidate.

Before promotion, lineage notes remain in the originating run file.

**Preference promotion.** Write to `preferences.md` only when at least one of these is true:

1. The user explicitly states a preference.
2. The user chooses, rejects, edits, or overrides a panel result.
3. The user asks the system to remember a preference.
4. The same inferred preference appears across multiple runs.

Mark inferred preferences as `inferred` and `low confidence` unless the user confirms them.

### Duplication Rules

Duplication is allowed only when each file plays a different role.

```yaml
run_file:
  stores: full local context and evidence from the run
  style: detailed
  authority: canonical event record

entity_file:
  stores: current cross-run state of a promoted formulation, lineage, or preference
  style: accumulated and updated
  authority: canonical entity record

index_file:
  stores: pointer, one-line summary, tags, and canonical location
  style: compact
  authority: navigation only
```

When updating an entity file from a run, add evidence — do not copy the whole run section. The index row points at the canonical file; it is not the canonical file.

Do not maintain duplicate canonical data in multiple formats. If JSONL cards or other derived artifacts are later used, they must be generated from Markdown, not edited separately.

### Raw Attempts Policy

Default stance:

```yaml
preserve_raw_attempts: false
preserve_attempt_summaries: true
preserve_selected_raw_outputs: true
```

Raw attempts are deep memory. Summaries are working memory.

Preserve raw attempts only when:

- The user asks.
- The run is high-value.
- The attempt is selected.
- The failure contains important signal.
- Rebranching requires exact text.

### Run ID Generation

Run IDs are Heroku-style two-word slugs: `run_<adjective>_<primate>`. They are collision-safe across parallel sessions (no shared sequential counter to race on), human-pronounceable (*"the silent capuchin run"* > *"run a3f8"*), and brand-on (primates everywhere).

**Loop IDs use the same scheme** with a different prefix: `loop_<adjective>_<primate>` (e.g., `loop_silent_capuchin`). Loops and runs live in different folders (`runs/` vs `loops/`) and the prefix differentiates them in cross-references. Same parallel-collision concern, same fix.

**Procedure** (orchestrator, at the start of every run):

1. Pick one adjective from the seed list below at random (or invent a new one — short, lowercase, single word).
2. Pick one primate-themed noun from the seed list below at random (or invent — same constraints).
3. Compose the candidate slug: `run_<adjective>_<primate>`.
4. Check whether `monkey-memory/runs/<slug>.md` already exists.
5. If it exists, append `_2`, `_3`, ... until the slug is unique on disk. (The user-facing display can still call it "the silent capuchin run, take 2.")
6. Use the resolved slug as `run_id` in the run file's frontmatter and as the filename.

**Seed adjectives** (~50, expand freely): brave, silent, curious, blue, swift, lazy, wise, tiny, vast, fierce, gentle, sleepy, loud, wild, bold, sly, calm, eager, jolly, plain, royal, sharp, quiet, witty, zen, restless, drunk, sober, golden, scarlet, rusty, polite, rude, anxious, smug, hungry, patient, jagged, soft, lean, stout, prim, glib, brisk, snappy, dim, vivid, salty, weary, plucky, ornery.

**Seed primate-themed nouns** (~50, expand freely): capuchin, gibbon, mandrill, tamarin, marmoset, lemur, baboon, orangutan, chimp, bonobo, macaque, langur, colobus, howler, gorilla, gelada, talapoin, drill, mangabey, sifaka, indri, aye-aye, loris, tarsier, galago, uakari, saki, douc, guenon, vervet, patas, proboscis, snubnose, titi, marmot (close enough), siamang, woolly, spider, squirrel, owl-monkey, night-monkey, slow-loris, brushtail, dusky, golden, silver, brown, white, black, red.

The orchestrator may invent more — keep adjectives short and concrete, primates real and recognizable. Don't go cute (no `run_silly_silly_pants_2`); aim for memorable.

**Backward compatibility.** Older runs use the legacy format `run_NNNN.md` (zero-padded sequential). The skill reads both formats. The runs index can mix both during transition. No migration is required; legacy runs stay legacy in their filenames.

**Cross-run references.** Always use the full slug, no abbreviation. `run_silent_capuchin/monkey_03` is correct; `silent/m03` is not (collides with the no-compression rule for monkey IDs from SKILL.md). Slug-only references (`run_silent_capuchin`) are fine when the context is unambiguous.

### Run File Template

Each run file should use predictable headings.

```markdown
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
memory_used: true
status: complete
tags: []
---

# Run run_<adjective>_<primate>: [Short Goal]

## Agent-Readable Summary

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

| Candidate | Topic | Curator | Operator | Skeptic | Humanizer | Σ |
|---|---|---:|---:|---:|---:|---:|

## Panel Decision

- **Best Overall:**
- **Most Useful:**
- **Most Beautiful:**
- **Most Surprising:**
- **Best Highlight:**
- **False Positive(s):**
- **Failure with Signal:**

## Monkey Parameter Cards

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

## Formulation Hypotheses

| Pattern | Origin | Confidence | Status |
|---|---|---|---|

## Convergence Analysis
<!-- Conditional: include when convergence (or its absence) was a meaningful signal, or when this run is part of a Loop Mode session. Otherwise omit. -->

## NEW Failure Patterns Logged
<!-- Conditional: include only when a new failure pattern surfaced this run. Otherwise omit. -->

## Lineage Updates
<!-- Conditional: include when a lineage threshold was crossed. Otherwise omit. -->

## User Judgment
<!-- Conditional: include only when the user judged or overrode the panel. Otherwise omit. -->

## Retrieval Capsule

## Selected Raw Outputs
<!-- Conditional: include only when raw monkey outputs are being preserved per the Raw Attempts policy. Otherwise omit. -->
```

**Section legend.**

- **Required (every run):** Frontmatter, Agent-Readable Summary, Score Matrix, Panel Decision, Monkey Parameter Cards, Best Highlights, Formulation Hypotheses, Retrieval Capsule.
- **Conditional (include only when the trigger fires):** Convergence Analysis, NEW Failure Patterns Logged, Lineage Updates, User Judgment, Selected Raw Outputs.
- **Don't add freelance H2 sections** beyond what's listed. If you have something extra to say, weave it into an existing section.

### Retrieval Capsule

Every run should include a compact retrieval capsule for future agents.

```markdown
## Retrieval Capsule

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
```

### Formulation File Template

Use one Markdown file per formulation family when the formulation becomes worth tracking.

```markdown
---
schema_version: 1
formulation_id: ""
name: ""
status: hypothesis
confidence: low
created: YYYY-MM-DD
last_used: YYYY-MM-DD
---

# Formulation: [name]

## Status

seed | hypothesis | supported | strong | cooldown | dormant | retired

## Confidence

low | medium | high

## Instruction Patch

```text

```

## Works Well For

-

## Weak For

-

## Observed Strengths

-

## Observed Weaknesses

-

## Evidence

| Run | Goal Type | Result | User Liked? | Notes |
|---|---|---|---|---|

## Recommended Pairings

-

## Anti-Pairings

-

## Cooldown Notes

## Rebranch Notes
```

### Lineage File Template

Use lineages for recurring branches, not every small run.

```markdown
---
schema_version: 1
lineage_id: ""
name: ""
status: active
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
---

# Lineage: [name]

## Summary

## Status

active | dormant | dead_end | retired

## Ancestors

-

## Key Descendants

-

## Strengths

-

## Risks

-

## Dormant Branches

-

## Dead Ends

-

## Recommended Rebranches

-

## Cross-References

-
```

### Loop Session File Template

A loop session is multiple back-to-back runs on one goal. Each loop is a full run with its own run file; the session also gets a wrapper file that records the memory-level shape of the session.

```markdown
---
schema_version: 1
loop_id: loop_<adjective>_<primate>      # e.g. loop_silent_capuchin
date: YYYY-MM-DD
goal: ""
goal_type: ""
per_loop_mode: standard
loops_attempted: 5
loops_completed: 5
stop_reason: loop_count_reached | cost_cap | convergence | user_interrupt
member_runs:
  - run_silent_capuchin
  - run_brave_gibbon
  - run_curious_mandrill
  - run_jolly_tamarin
  - run_swift_macaque
produced_under_skill_version: 0.4.5
---

# Loop loop_<adjective>_<primate>: [Short Goal]

## Session Summary

One paragraph: what the loop set out to do, what the memory learned across the session, whether it converged.

## Per-Loop Synthesis

| Loop | Run ID | Best Overall | Best Highlight | Notable Failure | New Formulation Hypotheses |
|---|---|---|---|---|---|

## Memory Movement

What changed in memory across this session. Format:

- Formulations strengthened: [name(s)] — moved from hypothesis → supported, or supported → strong.
- Formulations cooled: [name(s)] — used too often, on cooldown.
- Failures recurring: [pattern(s)] — surfaced in N loops.
- Highlights promoted: [list].
- Lineages updated: [list].
- Preferences observed: [list], with HOW/WHAT classification.

## Convergence Assessment

Was convergence detected? If yes, on which loop did the memory stop learning?

If no, how much further might the room go before saturating? (Best-effort guess from the Memory Keeper, not a promise.)

## Rebranch Suggestions for Next Session

-

## Cost Notes

Tokens, time, monkey calls if relevant.
```

### Preferences File Template

The project's `monkey-memory/preferences.md` and the optional shared `~/.monkeys-with-finger-paints/shared/preferences.md` use the same shape, with one difference: shared only ever contains HOW entries.

```markdown
# User Preferences

## Project Metadata

```yaml
project_path: <abs path>
shared_memory_enabled: true | false
last_updated: YYYY-MM-DD
```

## How-Preferences (Explicit)

Things the user explicitly stated about how they want output to read, sound, or be reasoned about. Domain-general.

```yaml
- text: "Reaches for blunt voices, not consultant voices."
  type: how
  origin: explicit
  confidence: high
  first_observed: run_silent_capuchin
  last_reinforced: run_jolly_tamarin
  cross_project_observation_count: 1
  promoted_to_shared: false
```

## How-Preferences (Inferred, Low Confidence)

Patterns the skill noticed but the user has not confirmed. Promote on confirmation; demote on override.

```yaml
- text: "Prefers shorter outputs over comprehensive ones."
  type: how
  origin: inferred
  confidence: low
  first_observed: run_brave_gibbon
  last_reinforced: run_brave_gibbon
  cross_project_observation_count: 1
  promoted_to_shared: false
```

## What-Preferences (Project-Specific)

Things the user stated about *this project's* subject matter. Stays per-project, never goes to shared.

```yaml
- text: "Wants the brand voice to be irreverent."
  type: what
  origin: explicit
  confidence: high
  first_observed: run_silent_capuchin
  last_reinforced: run_jolly_tamarin
```

## Disliked

Things the user has rejected or actively pushed back on. Treat as anti-signal — avoid when planning future troops.

```yaml
- text: "Generic multi-agent framing."
  type: how
  origin: explicit
  confidence: high
  observed_in: [run_curious_mandrill, run_swift_macaque]
```

## Override History

```yaml
total_runs: 0
overrides_applied: 0
override_rate: 0.0
window: last_10_runs
```

## Override Log

Per-override record. Lets the system learn which preferences keep getting reinforced and which keep getting overridden.

| Run | Panel Call | User Override | Preference Implicated | Action Taken |
|---|---|---|---|---|
```

The shared preferences file uses the same shape but contains only HOW entries (Explicit + Inferred), the disliked HOW patterns, and a rolling reinforcement log. WHAT-preferences and override-log specifics stay per-project.

### Runs Index Template

```markdown
# Runs Index

| Run ID | Date | Goal Type | Goal | Mode | Best Formulations | User Preference Signal | Canonical Run File | Notes |
|---|---:|---|---|---|---|---|---|---|
```

### Formulations Index Template

The formulations index lists both promoted and unpromoted formulation hypotheses. Promotion column distinguishes them; Canonical Location points at the truth source.

```markdown
# Formulations Index

| Formulation | Promotion | Status | Confidence | Works Well For | Risks | Evidence Count | Canonical Location | Cooldown? |
|---|---|---|---|---|---|---:|---|---|
```

`Promotion` is one of: `seed | unpromoted | promoted`.

For unpromoted formulations, `Canonical Location` points to the originating run section (e.g. `runs/run_0007.md#formulation-hypotheses`). For promoted formulations, it points to `monkey-memory/formulations/[name].md`.

### Highlights Index Template

The highlights index is a pointer table, not the source of truth. Highlights live inside their originating run file.

```markdown
# Highlights Index

| Highlight | Source Run | Source Candidate | Category | Goal Type | Canonical Location | Notes |
|---|---|---|---|---|---|---|
```

### Failures Index Template

Failures may be indexed globally, but the originating run remains canonical unless the failure pattern is promoted into its own file.

```markdown
# Failures Index

## [failure_pattern_name]

Pattern:

Usually caused by:

Avoidance:

Seen in:

Canonical examples:

Revisit when:
```

### Memory README Template

Every `monkey-memory/` folder gets a `README.md` at its root. The README is for any agent — human or otherwise — that walks into the memory *without* having read the skill spec. It tells them enough to navigate the folder, weight the evidence appropriately, and find the full spec if they want it.

This is a one-time write at bootstrap. The README does not get updated per-run. If the spec's persistence model ever changes meaningfully, an explicit migration pass updates the README; runs do not touch it.

The standard content is below. Bootstrap procedures should write it verbatim.

```markdown
# Monkey Memory — Monkeys with Finger Paints

This folder is the persistent memory of a Monkeys with Finger Paints run series. Every run leaves a structured record here. Future runs read it to bias their work; humans browse it to understand what's been tried; other agents can pull insights from it without invoking the skill itself.

If you arrived here without reading the skill, you don't need to. This README has enough to navigate.

## Structure

```
monkey-memory/
  .config.yaml       memory location config; says whether shared memory is enabled for this project
  runs/              one .md per run, canonical for what happened in that run
  loops/             (when present) one .md per Loop Mode session — wraps multiple member runs
  reflections/       (when present) saved memory-reflection summaries
  formulations/      .md files for promoted formulation hypotheses
  lineages/          .md files for evolutionary branches that recurred
  indexes/           navigation tables, never canonical truth
    runs.md          master index of all runs
    formulations.md  master index of all formulations (promoted + unpromoted)
    goals.md         goal types and run counts
    highlights.md     pointers to preserved beautiful highlights
    failures.md      recurring failure patterns
  preferences.md     user's taste — HOW (domain-general) and WHAT (project-specific), explicit and inferred
  entropy.md         rolling 10-run window detecting monoculture
  consolidated/      (when present) summaries of older runs to keep retrieval lean
```

If `.config.yaml` shows `shared_memory_enabled: true`, the user has also opted into a cross-project shared preferences store at `~/.monkeys-with-finger-paints/shared/preferences.md`. That file holds only HOW-preferences (about the user as a writer/thinker) that have been observed in 2+ projects. WHAT-preferences (about subject matter) stay per-project.

## How to Navigate

Read in priority order. Stop reading when you have what you need.

1. `indexes/runs.md` — table of every run, recent first.
2. `indexes/formulations.md` — filter by goal type if you're looking for formulation patterns.
3. `preferences.md` — for what the user likes (and shared preferences if `.config.yaml` enables them).
4. `indexes/failures.md` — for known traps.
5. `loops/` — only if you're auditing a multi-loop session.
6. Open individual `runs/run_XXXX.md` only when an index points you there. Each run file starts with an "Agent-Readable Summary" — read that section first if you're skimming.

## Source-of-Truth Rules

- `runs/*.md` is canonical for what happened in that run. Don't edit retroactively.
- `loops/*.md` is canonical for what happened across a Loop Mode session — references its member runs but does not duplicate them.
- `formulations/*.md` is canonical for the current cross-run state of a promoted formulation.
- `lineages/*.md` is canonical for branch history.
- `preferences.md` (project) is canonical for the user's taste in this project. Shared `preferences.md` (when enabled) is canonical only for promoted HOW-preferences (domain-general user taste seen in 2+ projects).
- `entropy.md` is canonical for recent monoculture detection.
- `reflections/*.md` is canonical for the saved snapshot of a memory reflection — but the underlying truth lives in the source files the reflection summarized.
- `indexes/*.md` are pointers and summaries, never the canonical truth.

When the same fact appears in two places, prefer the more specific canonical file.

## How to Weight Evidence

Every run file's frontmatter carries fields that affect how much you should trust its claims:

- `formulation_schema_version: 1` is legacy — pre-dates voice anchors, prose_register, and several other concepts. Treat any formulation claims from v1 runs cautiously.
- `formulation_schema_version: 2` is current.
- `coverage_status: unverified` means the run made claims about external coverage (saturation, novelty) without web verification. Do not promote unverified coverage claims to high confidence.
- `coverage_status: verified` means coverage claims were web-checked.
- `execution_mode: simulated_single_context` means the orchestrator generated monkey outputs in its own context rather than spawning real subagents. Formulation hypotheses from simulated runs cap at low confidence and should not be promoted alone.
- `execution_mode: native_parallel_subagents` or `native_serial_subagents` means real independent workers; evidence is stronger.
- `produced_under_skill_version` tells you which version of the spec produced the run. Older versions may have lacked features that later versions assume. v0.3.0 added Loop Mode, memory reflection, and cross-project shared preferences. v0.2.0 and earlier ran fully per-project with no looping.
- `loop_session` and `loop_position`: when set, this run was part of a back-to-back loop; cross-reference `loops/<loop_session>.md` for the session-level context.

## Parameter Cards

In current runs, a monkey's parameter card has three buckets:

- `monkey_personality`: role, stance, risk appetite, audience assumption, and success metric.
- `finger paint`: cognitive tools, constraints, domain lens, evidence requirements, fresh-chaos seed, source material, and failure mode to avoid.
- `painting_style`: style, voice anchor, format, length, structure, and register.

The useful shorthand is: monkey personality + finger paint = painting.

## Scoring Shorthand

Banana scale: `1 banana | 2 bananas | 3 bananas | 4 bananas` = low | medium | high | exceptional. Whole bananas only.

Banana counts drive portfolio gating (3+ bananas on goal_fit required for any portfolio entry, etc.). See the panel reference for thresholds if you need them.

## Want the Full Spec?

See `../SKILL.md` and `../references/`.
```

The README template should be rewritten only when the persistence model changes meaningfully. Day-to-day runs do not touch it.

## Retrieval Model

Retrieval is the brain of future runs. In a skill-only implementation, keep it simple and explicit.

### V1 Retrieval Mechanism

Use full-text search, tag matching, headings, and compact indexes.

Do not require embeddings or a vector database.

Search strategy:

1. Classify the new goal.
2. Search the runs index for the same or adjacent goal type.
3. Search the formulations index for matching goal affinities.
4. Read relevant formulation files.
5. Read at most 1-3 prior run summaries.
6. Read user preferences if subjective taste matters.
7. Check failures index for common traps.
8. Choose a few memory-derived formulations and reserve fresh chaos.

### Retrieval Discipline

- Prefer indexes over raw runs.
- Prefer summaries over full outputs.
- Prefer selected outputs over raw attempts.
- Read raw attempts only for rebranching, audit, or exact phrasing.
- Keep retrieved context small enough to leave room for the new troop.

### Retrieval Planning Summary

Before generating monkeys, prepare a compact plan:

```yaml
retrieval_summary:
  goal_type:
  memory_matches:
  useful_formulations:
  formulations_on_cooldown:
  relevant_failures:
  user_preferences:
  fresh_chaos_ratio:
  rebranch_candidates:
```

### Memory Consolidation

> *Consolidation ≠ Reflection.* Consolidation is a write — it summarizes older runs and leaves a pointer back, to keep the active retrieval set lean. Reflection (see "Memory Reflection Procedure" below) is a read — the skill walks memory and reports back to the user with no write. Different operations, different triggers. Consolidation runs when the corpus crosses size thresholds; Reflection runs when the user asks.

Memory compounds, but raw run files do not need to compound forever in the active retrieval set. As `monkey-memory/runs/` grows, retrieval costs grow with it — even with progressive disclosure, agent-readable summaries get longer to scan and indexes get longer to skim.

Trigger consolidation when any of these is true:

- `monkey-memory/runs/` contains more than 50 run files.
- The combined size of `monkey-memory/indexes/runs.md` exceeds ~5,000 tokens.
- The user explicitly requests a consolidation pass.
- Three or more runs have happened without anyone reading the older runs.

Consolidation procedure:

1. Identify a *period* to consolidate — typically the oldest 20-30 runs that haven't been read or referenced in recent work.
2. Spawn a consolidation subagent. Its job is to produce `monkey-memory/consolidated/<period_id>.md` containing:
   - A compact synthesis of what happened across those runs (one paragraph per goal type).
   - The formulation hypotheses that earned promotion during the period and survived to current status.
   - The failure patterns that recurred.
   - The user preference signals that solidified.
   - A small table mapping consolidated-period rows to the original run IDs (so the originals remain reachable for audit).
3. The original run files stay in `monkey-memory/runs/` — they remain canonical records. They stop being loaded by default during retrieval.
4. Update `monkey-memory/indexes/runs.md` so consolidated runs collapse to a single pointer line: `runs 0001-0027 → consolidated/period_001.md`.
5. Entropy memory's rolling window does not change — it still tracks last 10 runs.

After consolidation, retrieval defaults to:

- Read `monkey-memory/consolidated/*.md` (compact, summarized) instead of individual old run files.
- Read individual run files only when the consolidated summary points to a specific run worth opening.

The principle: canonical records are forever; active retrieval set is finite. Consolidation is summarization-with-pointer-back, never deletion.

If a user later asks "what happened in run_0014?" the consolidated period entry says "run_0014 was a positioning task that produced `compression_first` as a strong formulation hypothesis. See `runs/run_0014.md` for the full record." The original is still there, just not in the default retrieval flow.

**Depth is preserved, not summarized away.** This is worth being explicit about. The reasoning behind a winning candidate, the panel debate, the formulation hypothesis evidence, the user's selection signals — every piece of that lives in the original run file forever and is one click away whenever a future agent or user wants it. What consolidation compresses is the *active retrieval set*, not the memory itself. A run file that earned the label "this memory entry is excellent" stays excellent and stays accessible. The skill never deletes its own thinking.

A useful mental model: consolidated summaries are the table of contents. Run files are the chapters. Indexes are the page numbers. The book stays whole; what gets streamlined is how an agent navigates it.

## Memory Reflection Procedure

> *Reflection ≠ Consolidation.* Reflection is a read — the skill walks memory and reports back to the user. Consolidation (see above) is a write — it rolls older runs into summaries to keep the active retrieval set lean. Different operations, different triggers. Reflection runs when the user asks; Consolidation runs when the corpus crosses size thresholds.

Reflection is reading the memory, not writing to it. The user asks something like *"what has the room learned about my taste?"* or *"summarize the whiskey-naming runs"*. The skill walks the memory and synthesizes a report. No monkeys, no panel, no new run file.

### When the User Triggers Reflection

Detect by phrase or intent:

- *"what does the memory know..."*, *"what has the room learned..."*, *"reflect on memory"*, *"summarize the memory"*, *"memory check"*
- *"which formulations are winning"*, *"which failures keep showing up"*
- *"what does the room know about my taste"*

If the user's request would require *running* monkeys (any new task, any new goal, any "give me ideas for X"), it is not a reflection — route to the High-Level Workflow instead.

### Read Order

Read in this order. Stop when you have enough to answer.

1. **Project preferences** — `monkey-memory/preferences.md`. The user's recorded taste for this project.
2. **Shared preferences** (if `shared_memory_enabled: true`) — `~/.monkeys-with-finger-paints/shared/preferences.md`. The user's general taste across projects.
3. **Runs index** — `monkey-memory/indexes/runs.md`. Master list of every run.
4. **Formulations index** — `monkey-memory/indexes/formulations.md`. Filter for `Promotion: promoted` entries with `status: supported` or `status: strong`.
5. **Failures index** — `monkey-memory/indexes/failures.md`. Recurring patterns.
6. **Entropy memory** — `monkey-memory/entropy.md`. Recent monoculture state.
7. **Loop sessions** (if any) — `monkey-memory/loops/*.md`. Session-level retrospectives.
8. **Consolidated periods** (if any) — `monkey-memory/consolidated/*.md`. Older runs already digested.
9. **Specific run files** — only when the user scoped the question (specific lineage, specific formulation family, specific goal type) and an index pointed there. Read the Agent-Readable Summary section first.

If the user scoped the reflection (e.g. *"about whiskey naming specifically"*), filter all of the above to that goal type.

### Synthesis Format

Default to a memory-first report. Sections:

```markdown
## What the Memory Currently Believes

One short paragraph. State the headline observation in plain language.

## Strong Patterns

- [Plain-English pattern name]. [What it is, in one sentence.] [Evidence in prose: "seen in 5 runs across military, political, and labor settings"]. (Technical name: `<technical_name>`)
- ...

## Weakly-Supported Patterns

- [Plain-English pattern name]. [What it is.] [Evidence — "seen in 2 runs, both in scientific settings"]. *(Low confidence — needs more runs to confirm.)* (Technical name: `<technical_name>`)

## Recurring Failures

- [Plain-English alarm — what the failure feels like to the reader]. [Conditions where it shows up.] (Technical name: `<technical_name>`)

## Convergence and Saturation

What domains has the room saturated on? Where is the room still learning?

## Gaps the Memory Has Not Filled Yet

What hasn't been tried? What goal types have only one or zero runs? Useful for the user to know where the memory is thin.

## Open Branches

Rebranch candidates and dormant lineages worth revisiting.
```

### Translate Technical Names to Plain English

The reflection report is for the user, not for the memory's internal accounting. Lead with plain language; tuck the technical name in parens at the end of the bullet for anyone who wants to cross-reference.

**Wrong:**

> brushpaw_council_hinge — meeting where decision is made, named contemporary advisor argues survivable path on the record. n=5, status: strong.

**Right:**

> **The decision-room pattern.** The script centers on a meeting where a named human made a survivable call, with a named advisor on the record arguing for it. Strong — seen in 5 runs across military, political, scientific, architectural, legal, labor, and commercial settings. (Technical name: `brushpaw_council_hinge`.)

The technical name is a memory-traceability tool, not a user-facing identifier. Same rule the spec already applies to failure patterns (where `ai_speak_giveaway` becomes *"tripped the AI-speak alarm"* in user-facing prose). Apply it consistently to formulation names, meta-failure names, lineage names, and any other technical identifier that surfaces during reflection.

### Drop `n=` Notation

Statistical-style notation like *"n=5, status: strong"* is meaningful inside indexes and run files where automated retrieval is reading. It is **not** for user-facing reflection prose. The user shouldn't have to know what `n` stands for to understand the report.

**Wrong:** *"brushpaw_council_hinge — n=5, status: strong."*

**Right:** *"Strong — seen in 5 runs."* / *"The room has tested this enough times to be confident: 5 runs, all positive."* / *"Strong, across 5 runs."*

The numeric `n`, the `status: strong/supported/hypothesis` lifecycle field, the `cross_project_observation_count`, the `confidence: low/medium/high` field — all of these are real fields in the indexes and run files, used for retrieval logic. They stay in the data. They do not appear in reflection prose. Translate them.

A useful test: **read the reflection aloud.** If it sounds like a human telling another human what the room knows, it's right. If it sounds like a database dump, translate further.

### Citation Discipline

Every claim cites at least one specific run ID. *"Run_0007 and run_0014 both showed the user override 'polished' winners in favor of weirder runners-up"* is honest. *"The user prefers weirdness"* with no citation is gloss.

If a pattern shows in fewer than 3 runs, mark it explicitly as low-confidence. Cautious language is mandatory:

- "Plausible" / "looks like" / "the room seems to" — for 2-3 run patterns.
- "Consistently" / "across the memory" / "the room is convinced" — for 5+ run patterns.
- Never "always" / "definitely" / "the user wants" — those overstate.

### Tone

Same voice as the user-facing run output: confident where evidence supports it, honest about uncertainty, slightly playful. The memory is allowed to be honest about what it doesn't know yet. *"Twelve runs in, the room is convinced you reach for blunt voices. Less convinced about length preferences — the evidence splits. No data yet on long-form prose."*

Do not invent confidence. Do not invent patterns. Reflection's value is *honesty about the record*; making things up to sound impressive defeats the entire point of keeping a memory.

### Reflection Does Not Write

A reflection pass should not modify any memory files. It is a read. If the reflection itself would benefit from being preserved (e.g. the user says *"save that summary"*), write it to `monkey-memory/reflections/reflection_YYYYMMDD_NNN.md` where `NNN` is a 3-digit zero-padded sequence per day starting at `001`. This avoids collisions when the user reflects more than once on the same day. Never overwrite an existing memory file with the synthesis.
