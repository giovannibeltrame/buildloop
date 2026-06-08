---
name: build-gate
description: Phase ③ exit gate — a thin orchestrator that runs the 8-step build-gate sequence (ux-checker · simplify · code-checker · code-review · security-review · verify · PR review · UAT) in order, bounces a failure to the phase that owns it, and on full pass advances ③→④. Use when tdd reports a bp's implementation done.
---

# /buildloop:build-gate

Drive the build-gate (AGENTS.md §1.4). This skill owns the 8-step sequence below — run it in order; on pass advance the bp ③ → ④ and hand to ship. It is an orchestrator skill (not an agent) because the sequence mixes agents, skills, and human steps. Apply the rules by reference.

## Precondition

`buildloop current-phase <bp>` reads `③ Build it`.

## Sequence — run in order, stop on the first real failure

1. **`ux-checker`** agent — only if the diff touches UI (the `ux-checker` agent owns the routing); skip otherwise.
2. **`/simplify`** — apply quality cleanups.
3. **`code-checker`** agent — clarity, complexity, coverage classification, TDD-discipline audit.
4. **`/code-review`** — correctness + reuse findings.
5. **`/security-review`** — sensitive-path findings.
6. **`/verify`** — drive the running app to confirm the bp's scenarios hold.

Steps 1–6 are automated. Then the human steps:

7. **PR review** — open the PR (per the project's PR template, if any) at this point and request human review.
8. **UAT** — human stakeholder confirms the behavior in the real system.

## Bounce on failure (§1.4)

Route a failure to the phase that owns the defect, then stop:

- **impl wrong** (right scenario, wrong code) → stay at ③, hand back to `/buildloop:tdd`. No transition row (already ③); log a work row noting the bounce.
- **scenario wrong** (mis-modeled HOW) → `buildloop log <bp> "build-gate bounce" "scenario wrong" --to "② Plan it"`, hand to `/buildloop:build-plan`.
- **premise wrong** (WHAT/WHY off) → `buildloop log <bp> "build-gate bounce" "premise wrong" --to "① (Re)Think it"`, hand to `/buildloop:interview-me`.

## Pass

All 8 steps clean (UAT signed off) →
```
buildloop log <bp> "build-gate advances" "8-step sequence + UAT clean" --to "④ Ship it"
```
then hand off to `/buildloop:ship-in-prd`. The PR merge that triggers deploy is ship's act (owned by `ship-in-prd`), not this gate's.

## Notes

- Order is fixed — a cheap structural check (ux/simplify/code-checker) shouldn't wait behind an expensive one.
- ③ → ④ stops short of full automation by design: steps 7–8 need human sign-off.
