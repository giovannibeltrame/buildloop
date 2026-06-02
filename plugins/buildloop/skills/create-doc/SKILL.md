---
name: create-doc
description: From triage intent, create the next-sequence buildloop doc (epic / fix / hyp / cnst) at status 1 from a template. Use when a user message describes building, fixing, or changing something and agrees to start a doc (per the triage rule), or asks to file an epic / bugfix / hypothesis / constant.
---

# /buildloop:create-doc

Create a new buildloop doc of the right kind, correctly numbered and stubbed, per the doc kinds in [AGENTS.md §3.3](AGENTS.md), the triage rule in [§3.4](AGENTS.md), and the sequence scheme in [§5.3](AGENTS.md).

## Steps

1. Determine the **kind** from intent: `epic` (feature / improvement / audit), `fix` (bug), `hyp` (unverified empirical claim), `cnst` (platform truth). If ambiguous, ask — do not guess.
2. Get the next id:
   ```
   buildloop next-id <kind>
   ```
3. Create `docs/buildloop/<kind-dir>/<doc-id>.md` at **status 1** with the section skeleton the 2 → 3 gate ([§6.2](AGENTS.md)) will require — `Headline:`, `## WHAT problem we must solve`, `## WHAT we must build`, `## HOW system must behave`, `## Process log`. For a constant, the shape is headline / rationale / evidence / change log (§3.3).
4. Log the creation: `/buildloop:log <doc> "human, manual" "status 0 → 1 stub filed"`.
5. Hand off to `/buildloop:ddd-refine` to begin status 2.

## Notes

- Do not auto-fire on questions, exploration, or discussion (§3.4) — propose, then act on agreement.
- One headline sentence, no "and" (§6.2 criterion 1).
