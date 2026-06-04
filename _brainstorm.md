# Build Loop

(Re)Think it --> Plan it --> Build it --> Ship it --> Tweak it. Loop again.

Skills map
- audit <!-- ✦ TRIM, unused, remove -->
- create-doc <!-- ✦ TRIM, unused, remove -->
- ddd-refine <!-- ↪ MAPS rename "ddd", sub-invoked by build-plan -->
- implement <!-- ↪ MAPS rename "tdd", only executes build plan stepy by step using TDD, nothing more -->
- log <!-- ↪ MAPS improved, changed to write in new docs and better format (table) and WHO changed WHAT in max. 280 chars -->
- promote <!-- ↪ MAPS REPURPOSE "ship-in-prd" every feature bringed in prd MUST pass on quality gates, have e2e tests -->
- interview-me <!-- ✦ NEW never guess, always ask" discipline -->
- bdd <!-- ✦ NEW, sub-invoked by build-plan -->
- build-plan <!-- ✦ NEW -->
- measure <!-- ↪ MAPS to the hypotheses flow and closes hyp-loop -->
- make-prototypes <!-- ✦ NEW -->
- does-it-worth <!-- ✦ NEW -->
- tweak-it <!-- ✦ NEW -->
- simplify (CLAUDE)
- code-review (CLAUDE)
- security-review (CLAUDE)
- verify (CLAUDE)

Agents map
- think-checker <!-- ✦ NEW -->
- doc-validator <!-- ✦  ↪ MAPS rename "plan-checker" + make it simple on bp-xxxx (build-plan) -->
- code-checker  <!-- ✦ TRIM make it simple -->
- test-writer <!-- ✦ TRIM, unused, remove -->
- ux-checker  <!-- ✦ TRIM make it simple -->

Docs map
- epic-xxxx.md <!-- ✦ TRIM, unused, remove -->
- fix-xxxx.md <!-- ✦ TRIM, unused, remove -->
- cnst-xxxx.md <!-- ↪ MAPS rename "feature-document.md" e.g.: signals.md, alerts.md, radar.md, backtest.md. My main idea about this is a "living" doc: every tweak in a feature must update the feature document itself as minimal as possible - no dead docs or infinitely docs list that never get read (KISS, DRY). TBD: what keeps the gate: just integration and e2e-tests? Must acceptance criteria (BDD) stay alive in feature-document and if a tweak change it we update on it? -->
- hyp-xxxx.md  <!-- TRIM, unused, remove: hypotheses are a SECTION inside fc-xxxx -->
- fc-xxxx.md <!-- ✦ NEW feature candidate -->
- bp-xxxx.md <!-- ✦ NEW feature-build-plan -->
- CHANGELOG.md <!-- ✦ NEW -->

## Loop overview

```mermaid
flowchart TD
    NEW([New idea / problem]) --> IM
    CHG([Change to a shipped feature]) --> TI

    subgraph RD["① (Re)Discover"]
        IM[interview-me<br/>WHY · narrative · hypotheses · metrics] --> FC[(fc-xxxx.md<br/>feature-candidate)]
        FC --> MP[make-prototypes · lo-fi]
        MP --> DIW{does-it-worth?}
    end

    DIW -->|never| DROP([Drop · keep learning])
    DIW -->|not yet| PARK([Park candidate])
    DIW -->|yes| BPL

    subgraph PL["② Plan"]
        BPL[build-plan<br/>sub: ddd + bdd] --> BP[(bp-xxxx.md<br/>phases · WHAT/DDD · HOW/BDD)]
    end

    BP --> TDD

    subgraph BT["③ Build & Test"]
        TDD[tdd · red → green → refactor] --> QG{quality gates<br/>code-checker · doc-validator<br/>security · ux}
        QG -->|fail| TDD
        QG -->|pass| VER[verify · UAT]
    end

    VER --> BIP

    subgraph SH["④ Tweak / Ship"]
        BIP[ship-in-prd] --> FD[(feature-document.md<br/>+ CHANGELOG.md)]
        FD --> MEAS{measure<br/>hypothesis validated?}
    end

    MEAS -->|yes · rollout| DONE([Shipped])
    MEAS -->|not yet| TI
    MEAS -->|invalidated| IM

    TI[tweak-it<br/>bugfix or improvement?] --> TDD
    DONE -.next iteration.-> IM
```

## (Re)Think it

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

- phase gate
    - AGENT think-checker

## Plan it

- SKILL build-plan [YAGNI, KISS, DRY]
    - Outcome: feature-build-plan.md
        - phases, steps, tasks
        - WHAT we must build [invokes /ddd]
        - HOW system must behave (acceptance criteria) [invokes /bdd]

- SKILL ddd
    - Outcome: feature-build-plan.md
        - WHAT we must build
        - Ubiquitous Language

- SKILL bdd
    - Outcome: feature-build-plan.md
        - HOW system must behave
        - bdd scenarios (old AGENTS.md: 4.1 BDD scenario format - but in a table format)

- phase gate
    - AGENT plan-checker

## Build it

- SKILL tdd
    - Outcome: tests, code

- phase gate <!-- TBD: must be a new agent responsible for orchestrate all these steps? -->
    1. AGENT ux-checker (optional)
    2. SKILL security-review (optional) <!-- TBD: must be optional or always? -->
    3. SKILL simplify
    4. AGENT code-checker
    5. SKILL code-review <!-- TBD: validate intersection between simplify, code-checker and code-review: do either be removed? -->
    6. SKILL verify

## Ship it

- SKILL ship-in-prd
    <!-- TBD: acceptance criteria (unit, integration, e2e tests) must stay alive in feat doc? -->
    - Questions loop:
        - What changed? Which kind of change? [CHANGELOG.md]
        - Which are the currently behaviors or rules we must keep working? 
        - Which are strict techinical information we must know about this?
    - Outcome: feature-document.md, CHANGELOG.md

- SKILL measure
    - Are hypothesis validated or not?
    - invokes tweak-it (optional)
    - Outcome: Good enough for full rollout? yes, not yet or never (feature-document.md)

## Tweak it

- SKILL tweak-it
    - bugfix or improvement?
    - Outcome: tests, code, update docs (feature-document.md), invokes /build-plan
