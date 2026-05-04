# User Preferences

## Project Metadata

```yaml
project_path: /Users/nstied/Documents/monkeys-with-finger-paints
shared_memory_enabled: false
last_updated: null
```

## How-Preferences (Explicit)

Things the user explicitly stated about how they want output to read, sound, or be reasoned about. Domain-general — eligible for promotion to shared memory after observation in 2+ projects (when shared memory is enabled).

```yaml
# Example shape:
# - text: "Reaches for blunt voices, not consultant voices."
#   type: how
#   origin: explicit
#   confidence: high
#   first_observed: run_0007
#   last_reinforced: run_0042
#   cross_project_observation_count: 1
#   promoted_to_shared: false
```

## How-Preferences (Inferred, Low Confidence)

Patterns the skill noticed but the user has not confirmed. Promote on confirmation; demote on override.

```yaml
# - text: "Prefers shorter outputs over comprehensive ones."
#   type: how
#   origin: inferred
#   confidence: low
#   first_observed: run_0011
#   last_reinforced: run_0011
#   cross_project_observation_count: 1
#   promoted_to_shared: false
```

## What-Preferences (Project-Specific)

Things the user stated about *this project's* subject matter. Stays per-project. Never goes to shared.

```yaml
# - text: "Wants the brand voice to be irreverent."
#   type: what
#   origin: explicit
#   confidence: high
#   first_observed: run_0001
#   last_reinforced: run_0042
```

## Disliked

Things the user has rejected or actively pushed back on. Treat as anti-signal — avoid when planning future troops.

```yaml
# - text: "Generic multi-agent framing."
#   type: how
#   origin: explicit
#   confidence: high
#   observed_in: [run_0003, run_0019]
```

## Override History

```yaml
total_runs: 0
overrides_applied: 0
override_rate: 0.0
window: last_10_runs
```

## Override Log

| Run | Panel Call | User Override | Preference Implicated | Action Taken |
|---|---|---|---|---|
