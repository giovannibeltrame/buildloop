<!--
Build plan. TEMPLATE (working doc, phase ② Plan it).
`build-plan` creates it and orchestrates the fills:
  WHAT + Ubiquitous language ← ddd
  HOW (acceptance scenarios)  ← bdd
  Plan (phases/steps/tasks · critical files · trade-offs) ← plan agent
Lives in docs/buildloop/working/. Archived under working/archive/ on ship.
Mode is `full` (from think-gate) or `lite` (thin, from tweak-it).
The plan-gate validates: DDD + BDD present, every task unfolded to its minimum.
-->

# bp-NNNN: <headline, mirrors its fc>

Candidate: [fc-NNNN](fc-NNNN.md)
Mode: <full | lite>

## WHAT — domain (DDD)

### Ubiquitous language

| term | meaning |
|---|---|
| <term> | <one-line definition> |

### Models · rules · services

<the domain we must build: entities, invariants, services, boundaries>

## HOW — behavior (BDD)

<acceptance scenarios in strict BDD format as table rows: one behavior per row,
one Given/When/Then each (no And/But), mandatory layer tag. Each row = one test.>

| layer | scenario | given | when | then |
|---|---|---|---|---|
| <unit\|integration\|e2e> | <behavior name> | <single precondition> | <single action> | <single observable outcome> |

## Plan

### Phases · steps · tasks

<decomposition unfolded to its minimum, every task small enough to implement directly>

1. <phase>
   1. <step>
      - <task>

### Critical files

- <path>: <why it matters / what changes>

### Trade-offs

- <decision>: <alternative rejected and why>

## Log

<!-- build-plan writes the first row: buildloop log <doc> build-plan "created from fc-NNNN" --to "② Plan it" -->

| when | who | phase → | what |
|---|---|---|---|
