# Build Loop

We default load: BDD, DDD, DRY, Harness engineering, KISS, SDD, TDD, YAGNI

## Discover

- SKILL interview-me [YAGNI, KISS, DRY]
    - Questions loop:
        - WHAT you want to build and WHY?
        - What is the narrative that will make this loveable?
        - Which are the hypothesis for it?
        - Which metrics would help us decide if is it getting success?
    - Outcome: feature-candidate.md
- SKILL make-prototypes
    - Outcome: UX prototypes (lo-fi and hi-fi)
- SKILL does-it-worth [YAGNI, KISS, DRY]
    - Questions loop:
        - Does it worth to be build? (for each prototype)
        - Which prototypes best convey the narrative?
    - Outcome: feature-candidate.md (yes, not yet or never)

## Plan

- SKILL plan [YAGNI, KISS, DRY]
    - Outcome: feature-build-plan.md
        - phases, steps, tasks
        - WHAT we must build [DDD]
        - HOW system must behave (acceptance criteria) [BDD]

## Build & Test

- SKILL test-and-build [BDD, TDD]
    - Outcome: tests, code

## Tweak

- SKILL take-in-prd
    - Questions loop:
        - Which are the currently behaviors or rules we must keep working (constants)?
        - Which are strict techinical information we must know about this (documentation)?
    - Outcome: prd build + document (e2e or constant).md

- SKILL measure
    - A/B tests
    - Questions loop:
        - Are hypothesis from discover validated or not?
        - Good enough for full rollout?
    - Outcome: yes, not yet or never

- SKILL tweak-it
    - bugfix or improvement?
    - Outcome: tests, code, update docs, /take-in-prd