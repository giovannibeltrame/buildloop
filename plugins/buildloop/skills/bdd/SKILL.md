---
name: bdd
description: Phase ② — fill a build plan's HOW: acceptance scenarios in strict BDD table form, one behavior per row with a mandatory layer tag. Sub-invoked by build-plan, or run directly on a bp.
---

# /buildloop:bdd

Define how the system must behave. Write acceptance scenarios into the bp's `## HOW — behavior (BDD)` table, owning the BDD format ([AGENTS.md §4.1](AGENTS.md)). Do not restate the rule.

## Steps

1. Read the bp's WHAT (from `ddd`) and the fc it links.
2. For each behavior the feature must exhibit, write **one table row** (§4.1): `layer | scenario | given | when | then`. One Given, one When, one Then — no And/But. One behavior per row; five behaviors → five rows.
3. Set the **layer tag** per row by asking whether the behavior crosses component boundaries: `[unit]` (in-process logic), `[integration]` (across components), `[e2e]` (full system). The tag is mandatory.
4. **Pin telemetry.** Where a hypothesis from the fc maps to a metric, add an acceptance row that asserts the app **emits that metric's event/counter** ([§4.10](AGENTS.md)) — so instrumentation ships with the feature, not bolted on later.

## Notes

- One behavior per row is the discipline the plan-gate enforces (§6.3 criterion 2). A compound row is a refactor into several.
- Standalone: operates on whatever bp is handed to it.
