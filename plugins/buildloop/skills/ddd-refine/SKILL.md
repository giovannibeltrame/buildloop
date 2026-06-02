---
name: ddd-refine
description: Drive a doc through status 2 (refining) — raise one question per ambiguity, enforce the BDD scenario format, and route a clean doc to the doc-validator 2 → 3 gate. Use when refining an epic / fix / hyp / cnst that is at status 1 or 2.
---

# /buildloop:ddd-refine

Guard the WHAT. This skill is the status-2 guardian per [AGENTS.md §4.6](AGENTS.md) and the BDD format owner per [§4.1](AGENTS.md). Apply those rules; do not restate them.

## Steps

1. Read the doc and the product docs it touches ([§2](AGENTS.md)).
2. For every ambiguity, write **one question** into an `## Open Questions` section. Never guess; "implicit requirements" do not exist (§4.6).
3. Shape `## HOW system must behave` into strict BDD scenarios (§4.1): one Given / one When / one Then, no And/But, mandatory layer tag `[unit | integration | e2e]`. Raise the layer question per scenario (does this behavior cross component boundaries?).
4. Before proposing 2 → 3, check for open questions:
   ```
   buildloop open-questions <doc-path>
   ```
   If it reports `open-questions`, the doc moves to **2-Blocked** — do not propose 2 → 3.
5. When clean, invoke the **doc-validator** agent for the [§6.2](AGENTS.md) gate. On pass, flip 2 → 3 and `/buildloop:log` the transition; on fail, address the gap list and re-run.

## Notes

- The skill MUST NOT propose 2 → 3 while any open question exists (§4.6).
- Constants-conflict check (§6.2 criterion 8): if the doc's scope would break a `cnst-NNNN.md`, either redesign or include an explicit "supersedes cnst-NNNN" deliverable.
