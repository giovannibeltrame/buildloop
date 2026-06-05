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
- cnst-xxxx.md <!-- ↪ MAPS rename "feature-document.md" e.g.: signals.md, alerts.md, radar.md, backtest.md. My main idea about this is a "living" doc: every tweak in a feature must update the feature document itself as minimal as possible - no dead docs or infinitely docs list that never get read (KISS, DRY). -->
- hyp-xxxx.md  <!-- TRIM, unused, remove: hypotheses are a SECTION inside fc-xxxx -->
- fc-xxxx.md <!-- ✦ NEW feature candidate -->
- bp-xxxx.md <!-- ✦ NEW feature-build-plan -->
- CHANGELOG.md <!-- ✦ NEW -->

## Loop overview

```mermaid
flowchart TD
    NEW([New idea / problem]) --> IM
    CHG([Change to a shipped feature]) --> TI

    subgraph RT["① (Re)Think it"]
        IM[interview-me<br/>WHY · narrative · hypotheses · metrics] --> FC[(fc-xxxx.md<br/>feature-candidate)]
        FC --> MP[make-prototypes · lo-fi]
        MP --> DIW{does-it-worth?}
    end

    DIW -->|never| DROP([Drop · keep learning])
    DIW -->|not yet| PARK([Park candidate])
    DIW -->|yes| TKC{{phase gate<br/>think-checker}}

    TKC --> BPL

    subgraph PL["② Plan it"]
        BPL[build-plan<br/>sub: ddd + bdd] --> BP[(bp-xxxx.md<br/>phases · WHAT/DDD · HOW/BDD)]
    end

    BP --> PLC{{phase gate<br/>plan-checker}}
    PLC --> TDD

    subgraph BT["③ Build it"]
        TDD[tdd · red → green → refactor] --> BG{{phase gate<br/>ux · security · simplify<br/>code-checker · code-review · verify}}
        BG -->|fail| TDD
    end

    BG -->|pass| BIP

    subgraph SH["④ Ship it (no gate)"]
        BIP[ship-in-prd] --> FD[(feature-document.md<br/>+ CHANGELOG.md)]
        FD --> MEAS{measure<br/>hypothesis validated?}
    end

    MEAS -->|yes · rollout| DONE([Shipped])
    MEAS -->|not yet| TI
    MEAS -->|invalidated| IM

    subgraph TW["⑤ Tweak it (no gate)"]
        TI[tweak-it<br/>bugfix or improvement?]
    end
    TI -->|repoints to Plan it| BPL
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
        - WHAT/WHY + fc has narrative + hypotheses + metrics, prototype(s) exists and had been filtered, does-it-worth decision == "yes"

## Plan it

- SKILL build-plan [YAGNI, KISS, DRY]
    - Full or lite (lite comes from tweak-it and targets to be thin and fast)
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
        - HOW system must behave: acceptance criteria (integration, e2e tests in doc table form)
        - bdd scenarios (old AGENTS.md: 4.1 BDD scenario format - but in a table format)

- phase gate
    - AGENT plan-checker

## Build it

- SKILL tdd
    - Outcome: tests, code

- phase gate
    - AGENT build-checker
        1. AGENT ux-checker (optional)
        2. SKILL simplify
        3. AGENT code-checker
        4. SKILL code-review
        5. SKILL security-review
        6. SKILL verify

## Ship it

- SKILL ship-in-prd
    - Questions loop:
        - What changed? Which kind of change? [CHANGELOG.md]
        - Behaviors or rules we must keep working [acceptance criteria (unit, integration, e2e tests)]
        - Which are strict techinical information we must know about this?
    - Outcome: feature-document.md, CHANGELOG.md <!-- TBD: partial (users) rollout in prd with CI/CD (check possibility of CI/CD of github + aws ec2)? -->

- SKILL measure
    - Are hypothesis validated or not?
    - Outcome: Good enough for full rollout? yes, not yet or never (feature-document.md) + invokes tweak-it (optional)

- SKILL tweak-it
    - bugfix or improvement?
    - Outcome: tests, code, update docs (feature-document.md), invokes /build-plan (options full or lite) <!-- TBD: which option is better for decide full or lite: quantitative data about the fix or empirically by human / AI? -->
