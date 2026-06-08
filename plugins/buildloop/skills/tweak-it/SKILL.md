---
name: tweak-it
description: Phase ④ — change a shipped feature (bugfix or improvement) and route it back into the loop at the right depth. Runs a rule-anchored scope checklist: lite re-enters at ② Plan it, full at ① (Re)Think it. Use when changing a shipped feature, or when measure returns "not yet".
---

# /buildloop:tweak-it

Route a change to a shipped feature back into the loop at the cheapest correct depth. Where you re-enter *is* the lite/full decision. Apply the writing principles (AGENTS.md §3.4).

## Precondition

A shipped `feature-document.md` to change. Identify whether the change is a **bugfix** or an **improvement**.

## Scope-trigger checklist (rule-anchored)

Default is **LITE**. Escalate to **FULL** if **any** trigger fires:

- touches a constant/contract,
- crosses a component boundary (needs new integration/e2e scenarios),
- changes the data model, or
- moves a hypothesis or a metric.

Otherwise (a local bugfix or improvement) it stays **LITE**.

## Re-entry

- **LITE → ② Plan it.** Invoke `/buildloop:build-plan` in **lite** mode (thin, fast; WHY/narrative unchanged). It creates a new bp and the loop proceeds from planning.
- **FULL → ① (Re)Think it.** The premise/scope moved — invoke `/buildloop:interview-me` to re-sharpen the fc (re-interview → re-worth → full build-plan).

Make only the **minimal** update to the feature-document the change warrants; the full record goes in the new working docs and the CHANGELOG.

## Notes

- The re-entry skill (`build-plan` lite / `interview-me`) creates the new working doc and writes its first log row — `tweak-it` itself routes, it does not advance a phase.
- A bugfix is not a doc kind of its own — it travels the same loop as any change, entered here.
