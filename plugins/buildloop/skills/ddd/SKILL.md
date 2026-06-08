---
name: ddd
description: Phase ② — fill a build plan's WHAT (domain models, rules, services) and Ubiquitous Language. Owns the DDD anti-assumption discipline (never guess, always ask). Sub-invoked by build-plan, or run directly on a bp.
---

# /buildloop:ddd

Guard the WHAT. Define the domain in the bp's `## WHAT — domain (DDD)` section. This skill owns the **DDD anti-assumption discipline** for the project. Apply the writing principles (AGENTS.md §2).

## Anti-assumption discipline

The guardian of the WHAT:

- Every ambiguity becomes a **question**, never a guess.
- "I think the user probably means…" is forbidden — ask instead.
- **"Implicit requirements" do not exist.** If it's not in the doc, it's not agreed.
- Open questions block the plan-gate, so `build-plan` must not advance toward ② → ③ while any remain.

## Steps

1. Read the bp, the fc it links, and the project's product docs the work touches (if the project keeps any).
2. Build the **Ubiquitous Language** table — every domain term used in the plan with a one-line definition. Reuse the project's existing terms; do not invent synonyms.
3. Define **models · rules · services** — the entities, invariants, services, and boundaries the feature needs. Name what will be built, not just the behavior.
4. For every ambiguity, write **one question** into an `## Open Questions` section. Never guess; resolve or surface them — do not paper over them.

## Notes

- WHAT only — the HOW (acceptance scenarios) is `bdd`'s job; decomposition is the `plan` agent's.
- Standalone: operates on whatever bp is handed to it.
