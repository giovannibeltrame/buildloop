---
name: tweak-it
description: Phase ④: change a shipped feature (bugfix or improvement) and route it back into the loop at the right depth. Runs a rule-anchored scope checklist: lite re-enters at ② Plan it, full at ① (Re)Think it. Use when changing a shipped feature, or when measure returns "not yet".
---

# /buildloop:tweak-it

Route a change to a shipped feature back into the loop at the cheapest correct depth. Where you re-enter *is* the lite/full decision. Apply the writing principles (`${CLAUDE_PLUGIN_ROOT}/AGENTS.md` §1).

## Precondition

A shipped `feature-document.md` to change. Decide whether the change is a **bugfix** or an **improvement**.

## Scope-trigger checklist (rule-anchored)

Default is **LITE**. Escalate to **FULL** if **any** trigger fires:

- touches a constant or contract,
- crosses a component boundary (needs new integration/e2e scenarios),
- changes the data model, or
- moves a hypothesis or a metric.

A local bugfix or improvement stays **LITE**.

## Re-entry

- **LITE → ② Plan it.** Invoke `/buildloop:build-plan` in **lite** mode (thin and fast; WHY and narrative unchanged). It creates a new bp and the loop proceeds from planning.
- **FULL → ① (Re)Think it.** The premise or scope moved, so invoke `/buildloop:interview-me` to re-sharpen the fc (re-interview → re-worth → full build-plan).

Update the feature-document only as much as the change warrants. The full record goes in the new working docs and the CHANGELOG.

## Notes

- The re-entry skill (`build-plan` lite or `interview-me`) creates the new working doc and writes its first log row. `tweak-it` routes; it does not advance a phase.
- A bugfix is not a doc kind of its own. It travels the same loop as any change, entered here.
