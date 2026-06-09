---
name: bdd
description: Phase ②: fill a build plan's HOW: acceptance scenarios in strict BDD table form, one behavior per row with a mandatory layer tag. Owns the BDD scenario format for the whole project. Sub-invoked by build-plan, or run directly on a bp.
---

# /buildloop:bdd

Define how the system must behave. Write acceptance scenarios into the bp's `## HOW — behavior (BDD)` table. This skill is the **single source of truth for the BDD scenario format**: the plan-gate audits against it and `tdd` consumes the layer tags. Apply the writing principles (`${CLAUDE_PLUGIN_ROOT}/AGENTS.md` §1).

## BDD scenario format

Acceptance scenarios live as table rows, one behavior per row:

| layer | scenario | given | when | then |
|---|---|---|---|---|
| `[unit \| integration \| e2e]` | one-line behavior name | single precondition | single action | single observable outcome |

- One Given, one When, one Then. **No `And`, no `But`.** Five behaviors make five rows.
- The **layer tag is mandatory**: `[unit]` (in-process logic), `[integration]` (across components), `[e2e]` (full system). Set it by asking whether the behavior crosses component boundaries. Invariant scenarios in a feature-document are always `[e2e]`.
- Strict format, no parser today. This keeps the option to plug in a BDD runner later without rewriting docs.

## Steps

1. Read the bp's WHAT (from `ddd`) and the fc it links.
2. For each behavior the feature must exhibit, write one row in the format above. A compound row is a refactor into several.
3. Set each row's layer tag.
4. **Pin telemetry.** Where a hypothesis from the fc maps to a metric, add an acceptance row asserting the app **emits that metric's event/counter** (the telemetry seam, §2.8), so instrumentation ships with the feature rather than bolted on later.

## Notes

- One behavior per row is the discipline the plan-gate enforces. Keep rows atomic.
- Standalone: operates on whatever bp is handed to it.
