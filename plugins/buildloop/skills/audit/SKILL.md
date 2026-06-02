---
name: audit
description: Run a project-wide buildloop audit (agents, coverage, constants) and file a sibling doc per gap, linked back to an umbrella epic. Use when the user asks to audit the agents / coverage / platform truths, or to refresh the buildloop inventory.
---

# /buildloop:audit

Sweep the project for drift and file each gap as a tracked sibling, enforcing the umbrella rule in [AGENTS.md §3.3](AGENTS.md) (every sibling links back; the umbrella lists each sibling).

## Steps

1. Run the project's drift checks — the same ones its pre-merge guard runs. A project wires these in `AGENTS.md` or its CI config; typical sweeps are:
   - **Agent rubric** — every gate agent under `.claude/agents/` is well-formed and routed.
   - **Coverage audit** — every product source file is at 100% or on a tracked debt ledger (§4.4).
   - **Constants e2e** — every Active `cnst-NNNN.md` has e2e evidence (§3.3).

   If the project has no scripted checks yet, perform the sweep by reading the relevant files directly.
2. For each gap, file a sibling doc via `/buildloop:create-doc` (a `fix-NNNN.md` for an agent/coverage gap, a `cnst-NNNN.md` for a harvested platform truth), and add a back-link to the umbrella epic.
3. Record the inventory in the umbrella epic's `## Audit findings` and grow its `## Sibling docs` list. `/buildloop:log` the audit pass.

## Notes

- Bidirectional links are mandatory — the 2 → 3 gate rejects orphan references in either direction (§3.3).
- Keep the umbrella an umbrella: it tracks and links siblings; the work happens in the siblings.
