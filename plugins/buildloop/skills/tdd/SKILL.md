---
name: tdd
description: "Phase ③: drive a build plan's implementation with red → green → refactor, owning the failing test (RED) as well as the code. Owns the project's red-first discipline. Tagged commits, one test at a time, scenarios in sequence; hands off to the build-gate. Use when implementing a plan-gated (③ Build it) bp."
---

# /buildloop:tdd

Own the build. This skill writes the failing test and the code, and is the **single source of truth for the project's TDD discipline** (`code-checker` audits against it). Apply the writing principles (`${CLAUDE_PLUGIN_ROOT}/AGENTS.md` §1).

## Precondition

The bp must be past the plan-gate: `buildloop current-phase <bp>` reads `③ Build it`. If not, stop and point at `/buildloop:build-plan`.

## Red-first (non-negotiable)

Tackle the bp's HOW scenarios in sequence, one test at a time. One scenario unfolds into N tests, each correctness condition its own assertion. For each test:

1. **Write the failing test yourself.** No separate test-writer; `tdd` owns RED. Run it. Observe **RED**, failing for the right reason rather than a setup or import error. Commit with a `RED —` tag (doc-id `bp-NNNN`). The `RED —` / `GREEN —` / `refactor —` commit-tag convention is this skill's; `code-checker` audits red-first discipline by greping for it.
2. Write the **minimum** code to flip **GREEN**. Nothing more. Commit with a `GREEN —` tag.
3. **Refactor** with all prior tests green. Commit with a `refactor —` tag if anything changed.

Locks:

- **One test at a time.** The cycle is per individual test, not per scenario or file. Writing five tests then code that flips them all at once violates TDD even when end-state coverage is identical.
- **Scenarios are tackled in sequence.** Tests never seen RED don't count.

Place each test in the layer matching its scenario tag (`[unit | integration | e2e]`), following the project's own test layout and naming convention. Where a bdd row pins "emits metric X", instrument it now so the metric ships with the feature (the telemetry seam, §2.8).

**Characterization exemption.** A pure coverage-backfill of *existing, unmodified* production code cannot observe a meaningful RED. It is exempt from the per-test loop only when **no production code changes**, and must instead: (1) declare itself in the log; (2) drive the real red→green at the coverage-gate level (the file moves from under the project's coverage bar to meeting it); (3) prove each test bites via a mutation spot-check (break the line, confirm the test fails, revert).

## Hand-off

When every scenario is green and `/simplify` and `/code-review` are clean in-thread, hand off to `/buildloop:build-gate`. The gate advances ③ → ④, not this skill. Record progress with work rows: `buildloop log <bp> tdd "<green: scenario N>"`.

## Notes

- Standalone: runs on a given file, ungated.
