---
name: implement
description: Drive a doc through status 5 — a thin TDD orchestrator that sequences red → green → refactor with tagged commits and runs the mandatory quality gates before handing off to review. Use when implementing a planned (status 4) epic or bugfix.
---

# /buildloop:implement

Orchestrate status-5 implementation. This skill owns the TDD loop and commit discipline; it delegates the quality gates to the built-in skills and review agents. Apply the rules by reference — do not restate them.

## TDD loop (per [AGENTS.md §4.5](AGENTS.md))

Tackle scenarios sequentially; one test at a time. For each test:

1. Write the failing test. Run it. Observe **RED** (failing for the right reason). Commit with a `RED —` tag ([§5.1](AGENTS.md)).
2. Write the minimum code to flip **GREEN**. Commit with a `GREEN —` tag.
3. Refactor with all prior tests green. Commit with a `refactor —` tag if anything changed.

Place each test in the layer matching its scenario tag ([§4.2](AGENTS.md)) under the [§4.3](AGENTS.md) naming. `/buildloop:log` each status transition.

## Gates (per [AGENTS.md §3.5](AGENTS.md))

- **Before 5 → 6 (mandatory)**: run `/simplify` then `/code-review`.
- **At 5 → 6**: the `code-checker` agent, plus `security-reviewer` (sensitive paths) and `ux-checker` (UI). Open the PR at this transition (§5.2).
- **At 6 → 7**: `/verify` drives the running app to confirm the scenarios hold.

## Notes

- The implementer can lean on the `test-writer` agent to pin acceptance checks and the `code-checker` agent for the coverage/clarity audit.
- Tests never seen RED do not count (§4.5).
