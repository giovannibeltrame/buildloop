# Build Loop

We default load: BDD, DDD, DRY, Harness engineering, KISS, SDD, TDD, YAGNI

Skills map
    - audit <!-- ✦ TRIM, unused, remove -->
    - create-doc <!-- ✦ TRIM, unused, remove -->
    - ddd-refine <!-- ↪ MAPS rename "ddd", sub-invoked by build-plan -->
    - implement <!-- ↪ MAPS rename "tdd", only executes build plan stepy by step using TDD, nothing more -->
    - log <!-- ↪ MAPS improved, changed to write in new docs and better format (table) and WHO changed WHAT in max. 280 chars -->
    - promote <!-- ↪ MAPS rename "bring-in-prd" -->
    - interview-me <!-- ✦ NEW -->
    - bdd <!-- ✦ NEW, sub-invoked by build-plan -->
    - build-plan <!-- ✦ NEW -->
    - measure <!-- ↪ MAPS to the hypotheses flow and closes hyp-loop -->
    - simplify (CLAUDE) <!-- TBD -->
    - code-review (CLAUDE) <!-- TBD -->
    - verify (CLAUDE) <!-- TBD -->

Agents map
    - code-checker  <!-- TBD -->
    - doc-validator <!-- TBD -->
    - test-writer <!-- ✦ TRIM, unused, remove -->
    - security-reviewer (CLAUDE) <!-- TBD -->
    - ux-checker <!-- TBD -->

Docs map
    - epic-xxxx.md <!-- ✦ TRIM, unused, remove -->
    - fix-xxxx.md <!-- ✦ TRIM, unused, remove -->
    - cnst-xxxx.md <!-- ↪ MAPS rename "feature-document.md" e.g.: signals.md, alerts.md, radar.md, backtest.md -->
    - hyp-xxxx.md  <!-- TBD -->
    - fc-xxxx.md <!-- ✦ NEW feature candidate -->
    - bp-xxxx.md <!-- ✦ NEW feature-build-plan -->
    - CHANGELOG.md <!-- ✦ NEW -->

## (Re)Discover

- SKILL interview-me [YAGNI, KISS, DRY]
    - Questions loop:
        - WHAT you want to build and WHY?
        - What is the narrative that will make this loveable?
        - Which are the hypothesis for it?
        - Which metrics would help us decide if is it getting success?
    - Outcome: feature-candidate.md
- SKILL make-prototypes
    - Outcome: UX prototypes (lo-fi)
- SKILL does-it-worth [YAGNI, KISS, DRY]
    - Questions loop:
        - Does it worth to be build? (for each prototype)
        - Which prototypes best convey the narrative?
    - Outcome: feature-candidate.md (yes, not yet or never)

## Plan

- SKILL build-plan [YAGNI, KISS, DRY]
    - Outcome: feature-build-plan.md (replaces epic/bugfix docs)
        - phases, steps, tasks
        - WHAT we must build [DDD]
        - HOW system must behave (acceptance criteria) [BDD]

## Build & Test

- SKILL tdd [BDD, TDD]
    - Outcome: tests, code

## Tweak

- SKILL bring-in-prd
    - Questions loop:
        - What changed? Which kind of change? [CHANGELOG.md]
        - Which are the currently behaviors or rules we must keep working?
        - Which are strict techinical information we must know about this?
    - Outcome: feature-document.md, CHANGELOG.md

- SKILL measure
    - Questions loop:
        - Are hypothesis from discover validated or not?
        - Good enough for full rollout?
    - Outcome: yes, not yet or never (feature-document.md)

- SKILL tweak-it
    - bugfix or improvement?
    - Outcome: tests, code, update docs (feature-document.md), invokes /bring-in-prd
