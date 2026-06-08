---
name: tdd
description: Phase ③ — drive a build plan's implementation with red → green → refactor, owning the failing test (RED) as well as the code. Tagged commits, one test at a time, scenarios in sequence; hands off to the build-gate. Use when implementing a plan-gated (③ Build it) bp.
---

# /buildloop:tdd

Own the build. This skill writes the failing test *and* the code, sequencing red → green → refactor per [AGENTS.md §4.5](AGENTS.md). Apply the rules by reference — do not restate them.

## Precondition

The bp must be past the plan-gate: `buildloop current-phase <bp>` reads `③ Build it`. If not, stop and point at `/buildloop:build-plan`.

## TDD loop (§4.5)

Tackle the bp's HOW scenarios **sequentially**; one test at a time. One scenario unfolds into N tests — each correctness condition is its own assertion. For each test:

1. **Write the failing test yourself** (no separate test-writer — `tdd` owns RED). Run it. Observe **RED**, failing for the right reason, not a setup/import error. Commit with a `RED —` tag ([§5.1](AGENTS.md), doc-id `bp-NNNN`).
2. Write the **minimum** code to flip **GREEN**. Nothing more. Commit with a `GREEN —` tag.
3. **Refactor** with all prior tests green. Commit with a `refactor —` tag if anything changed.

Place each test in the layer matching its scenario tag ([§4.2](AGENTS.md)) under the [§4.3](AGENTS.md) naming convention. Where a bdd row pins "emits metric X", instrument it now so the metric ships with the feature ([§4.10](AGENTS.md)).

## Hand-off

When every scenario is green and `/simplify` + `/code-review` are clean in-thread, hand off to `/buildloop:build-gate`. The gate — not this skill — advances ③ → ④. Record progress with work rows: `buildloop log <bp> tdd "<green: scenario N>"`.

## Notes

- **Tests never seen RED don't count** (§4.5). For a pure coverage-backfill of unchanged code, follow the characterization exemption (§4.5): declare it in the log, drive the coverage-gate red→green, and spot-check each test by mutation.
- Standalone: runs on a given file, ungated.
