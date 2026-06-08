---
name: ddd
description: Phase ② — fill a build plan's WHAT (domain models, rules, services) and Ubiquitous Language, guarding against assumptions (never guess, always ask). Sub-invoked by build-plan, or run directly on a bp.
---

# /buildloop:ddd

Guard the WHAT. Define the domain in the bp's `## WHAT — domain (DDD)` section, applying the DDD anti-assumption discipline ([AGENTS.md §4.6](AGENTS.md)) and the writing principles ([§4.7](AGENTS.md)). Do not restate the rules.

## Steps

1. Read the bp, the fc it links, and the product docs the work touches ([§2](AGENTS.md)).
2. Build the **Ubiquitous Language** table — every domain term used in the plan with a one-line definition. Reuse the project's existing terms; do not invent synonyms.
3. Define **models · rules · services** — the entities, invariants, services, and boundaries the feature needs. Name what will be built, not just the behavior.
4. For every ambiguity, write **one question** into an `## Open Questions` section (§4.6). Never guess; "implicit requirements" do not exist. Open questions block the plan-gate, so resolve or surface them — do not paper over them.

## Notes

- WHAT only — the HOW (acceptance scenarios) is `bdd`'s job; decomposition is the `plan` agent's.
- Standalone: operates on whatever bp is handed to it.
