---
name: interview-me
description: Entry skill for phase ① (Re)Think it — interview the user to produce a feature candidate (fc) with WHY, narrative, hypotheses, and metrics. "Never guess, always ask." Use when a user describes a new idea or problem to build, or wants to re-sharpen an existing fc.
---

# /buildloop:interview-me

Open phase ① by turning an idea into a feature candidate. Guard the WHAT with the anti-assumption discipline the `ddd` skill owns — never guess, always ask. Apply the writing principles (AGENTS.md §2); do not restate any rule.

## Steps

1. **New or re-sharpen?** If re-sharpening an existing fc (e.g. `does-it-worth` returned "not yet"), open it. Otherwise create one:
   ```
   buildloop next-id fc
   ```
   Copy the plugin's `templates/fc-xxxx.md` to `docs/buildloop/working/fc-NNNN.md` (per the doc map, §1.5).
2. **Run the questions loop**, one question per ambiguity — never guess. Read the project's product docs the idea touches first (if the project keeps any). Fill the fc sections:
   - **Why** — WHAT you want to build and WHY; the problem worth solving, not a restatement of the headline.
   - **Narrative** — what makes it lovable.
   - **Hypotheses** — the falsifiable claims the feature bets on.
   - **Metrics** — for each hypothesis, the metric, its success threshold, and the app **event/counter** that emits it (the telemetry seam, §1.8 — naming it here is what lets `measure` judge it later).
3. **Write the first log row** (the template ships an empty table):
   ```
   buildloop log docs/buildloop/working/fc-NNNN.md interview-me "created" --to "① (Re)Think it"
   ```
   On a re-sharpen, append a work row instead (no `--to`).
4. Hand off to `/buildloop:make-prototypes`.

## Notes

- One headline sentence, no "and" (the think-gate's first criterion).
- Anything not in the doc is not agreed. Capture unresolved ambiguities as questions; do not advance on a guess.
- Standalone: works anywhere; with no context it starts a fresh fc.
