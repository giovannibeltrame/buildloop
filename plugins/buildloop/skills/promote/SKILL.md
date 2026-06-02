---
name: promote
description: Promote a constant from Proposed to Active once its e2e test exists, refusing (and naming the missing evidence) otherwise. Use when a cnst-NNNN.md is ready to activate, or when closing out a constant's implementation.
---

# /buildloop:promote

Flip a constant's label from Proposed to Active per the constant lifecycle in [AGENTS.md §3.3](AGENTS.md) — but only once it is backed by an e2e test, the same evidence the Phase-4 drift guard requires.

## Steps

1. Check eligibility:
   ```
   buildloop can-promote docs/buildloop/constants/<cnst-id>.md tests/e2e
   ```
2. If it reports `BLOCKED`, **refuse** the promotion and name the missing e2e evidence; the constant stays Proposed until its `tests/e2e/` test exists.
3. If promotable, change the doc's `Label:` from `**Proposed**` to `**Active**`, and `/buildloop:log` the transition (status 7 → 8 / Proposed → Active).

## Notes

- Activating a constant without an e2e test would break the drift guard's "Active constant has e2e evidence" check — this skill is the gate that prevents that.
- A constant becomes Active only at status 8 Completed (§3.3).
