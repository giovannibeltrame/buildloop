<!--
BuildLoop operating manual (TEMPLATE).

Drop this file at your repository root as `AGENTS.md`. The buildloop plugin's
skills and agents reference this file by section number (e.g. "AGENTS.md §1.4"),
so keep the section numbering intact when you edit.

Scope: this file is the project-agnostic buildloop methodology: the phase/loop
map and the gate contracts, identical for every project that adopts buildloop.
It deliberately does NOT prescribe your project's own conventions (test layout,
naming, coverage policy, product docs); those stay with your project. Method
rules live in their owning skill (BDD → bdd, TDD → tdd, DDD → ddd, the
build-gate sequence → build-gate, deploy → ship-in-prd) and gate pass-criteria
live in their gate agent (think-gate, plan-gate). This file points at those
owners; it does not restate them.
-->

# Agent Instructions

This file is the canonical operating manual for the buildloop workflow. It is tool-agnostic; every agent, skill, and human contributor reads from here. `CLAUDE.md` at the root is a thin pointer to this file plus the few non-negotiables that must always load.

## 1. The build loop

(Re)Think it → Plan it → Build it → Ship it. Loop again.

### 1.1 Phases and gates

```mermaid
flowchart LR
    NEW([New idea]) --> RT
    CHG([Change to shipped]) --> SH

    RT["① (Re)Think it"] --> G1{{think-gate}}
    G1 --> PL["② Plan it"]
    PL --> G2{{plan-gate}}
    G2 --> BT["③ Build it"]
    BT --> G3{{build-gate}}
    G3 --> SH["④ Ship it"]
    SH --> DONE([Shipped])

    G3 -.fail · impl.-> BT
    G3 -.fail · scenario.-> PL
    G3 -.fail · premise.-> RT
    SH -.tweak · lite.-> PL
    SH -.tweak · full / invalidated.-> RT
    DONE -.next iteration.-> RT
```

- **New idea / problem** enters at ① via `interview-me`.
- **Change to a shipped feature** enters via `tweak-it`, which re-enters the loop at ② (lite) or ① (full).

### 1.2 State lives in the log table

There is no `Status:` field. Each doc carries a `## Log` table that is the single source of truth for "where are we."

- **Fixed four columns**: `when | who | phase → | what`.
- **Two row kinds.** A *work* row is intra-phase progress (`phase →` is `—`). A *transition* row carries `<From> → <To>`. **Current phase = the To-side of the most recent transition row.**
- **Append-only, single writer.** Only the `log` skill writes the table, newest at the bottom; that is what makes "last transition = state" trustworthy. The append mechanics live in that skill.
- **No top-of-doc cache.** Derive the phase from the log; `buildloop current-phase <doc>` does this deterministically.
- **Canonical phase tokens**: `① (Re)Think it · ② Plan it · ③ Build it · ④ Ship it`.

### 1.3 The phases

Each phase names the skills that run in it and the artifact it produces. Skills carry their own method rules; this section is the WHAT and WHY.

| Phase | Skills | Produces |
|---|---|---|
| **① (Re)Think it** | `interview-me` (WHY · narrative · hypotheses · metrics) → `make-prototypes` (lo-fi) → `does-it-worth` (verdict) | `fc-NNNN.md` |
| **② Plan it** | `build-plan` orchestrates `ddd` (WHAT) + `bdd` (HOW) + the `plan` agent (decomposition) | `bp-NNNN.md` |
| **③ Build it** | `tdd` (red → green → refactor) → `build-gate` | tests + code |
| **④ Ship it** | `ship-in-prd` (deploy + release) → `measure` (hypothesis verdict) → `tweak-it` (bugfix / improvement) | `feature-document.md` + `CHANGELOG.md` |

### 1.4 Gates

A gate is a **read-only verdict function of doc state**, never of which skill ran. Because it depends only on artifacts-present-and-valid and never writes, it works the same whether spawned by the advancing skill or invoked standalone (`/<gate> <doc>`); on pass, the write of the transition row is always the separate step owned by whoever advances.

**Already-past guard**: a gate first reads `buildloop current-phase`; if the doc is already past its phase, it returns a one-line "already at phase X" and skips the rubric.

| Gate | Kind | Validates | Where the rule lives | Emits |
|---|---|---|---|---|
| `think-gate` | agent (read-only) | the fc is complete | `think-gate` agent | `① → ②` |
| `plan-gate` | agent (read-only) | the bp is valid (DDD + BDD + decomposition) | `plan-gate` agent | `② → ③` |
| `build-gate` | skill (orchestrator) | the 8-step sequence is clean | `build-gate` skill | `③ → ④`, else bounce |

**Build-gate bounce** routes a failure to the phase that owns the defect: implementation wrong → ③ (`tdd`); scenario mis-modeled → ② (`build-plan`); premise wrong → ① (`interview-me`).

### 1.5 Doc kinds and lifecycle

| Doc | Kind | Path | Role |
|---|---|---|---|
| `fc-NNNN.md` | feature candidate | `docs/buildloop/working/` | WHY · narrative · hypotheses · metrics · prototypes · verdict |
| `bp-NNNN.md` | build plan | `docs/buildloop/working/` | phases · WHAT/DDD · HOW/BDD · decomposition |
| `feature-document.md` | living doc | `docs/buildloop/living/` | one per feature; hypotheses + metrics + invariants + release |
| `CHANGELOG.md` | change history | repo root | WHO changed WHAT, newest first |

- **fc and bp are working (transient) docs.** They hold the full log until ship, then archive under `docs/buildloop/working/archive/`.
- **feature-document.md is the living doc.** `ship-in-prd` distills the fc + bp hypotheses, metrics, and invariants into it, so `measure` has something to read. It keeps only its **last transition**; change history goes to `CHANGELOG.md`.
- **No constants doc.** Invariants the system must uphold are declared in a feature-document's **"Invariants (e2e-guarded)"** section and guarded by e2e tests.

### 1.6 Skill contracts

Each skill declares **Requires (state) / Produces / Standalone fallback**. *Requires* is a precondition on state (artifact present + phase read from the log), never "skill X ran first." Skills sequence themselves because one skill's Requires is another's Produces; neither names the other.

| Skill | Requires (state) | Produces | Standalone fallback |
|---|---|---|---|
| `interview-me` | none (entry); or an existing fc to re-sharpen | fc (WHY · narrative · hypotheses · metrics) | works anywhere; creates a new fc |
| `make-prototypes` | fc exists | lo-fi prototypes in fc | asks for / stubs a minimal fc |
| `does-it-worth` | fc + prototypes present | verdict in fc | refuses; names what's missing |
| `build-plan` | fc past think-gate (②); or lite entry from tweak-it | bp (phases · WHAT · HOW) | runs on a given fc; flags if gate not passed |
| `ddd` | bp (or invoked by build-plan) | WHAT + ubiquitous language in bp | operates on the doc handed to it |
| `bdd` | bp (or invoked by build-plan) | acceptance scenarios (table) in bp | operates on the doc handed to it |
| `plan` (agent) | bp (or invoked by build-plan) | decomposition + critical files + trade-offs in bp | operates on the doc handed to it |
| `tdd` | bp past plan-gate (③) | tests + code (red→green→refactor) | runs on a given file, ungated |
| `simplify · code-review · security-review · verify` | a diff (verify also needs a running app + bp scenarios) | cleanups / findings / UAT verdict | run on any diff |
| `ship-in-prd` | build-gate green (per log) | feature-document + CHANGELOG | refuses; names the missing gate |
| `measure` | feature-document w/ hypotheses + metrics + **live prod data** | rollout verdict (yes / not yet / invalidated) | refuses if no metrics defined |
| `tweak-it` | a shipped feature-document | full/lite build-plan invocation (sets re-entry phase) | operates on the named feature |
| `log` | a doc (creates the `## Log` table if missing) | appended row (work or transition) | creates the table |

**Two Requires that nothing else Produces (external inputs, by design):**
- `measure` needs **live prod data**, from telemetry on the shipped cohort, not any doc (§1.8).
- `ship-in-prd` needs the **deploy + feature-flag** mechanism: infra, not a skill output (owned by the `ship-in-prd` skill).

Everything else chains: an upstream Produces satisfies each Requires.

### 1.7 Auto-suggest at triage

When a user message describes building, fixing, or changing something, Claude proposes the entry skill before writing code: `interview-me` for a new idea, `tweak-it` for a change to a shipped feature. User confirms or redirects. No auto-firing; the explicit skill invocation is always available. Questions, exploration, and discussion do not trigger the suggestion.

### 1.8 Telemetry seam

The hypothesis loop closes only if a metric defined up front is the same one judged at the end. `measure` needs live prod data, which no skill produces; this chain supplies it:

- Metrics + success threshold are defined in `interview-me` (fc): the "which metrics tell us success" question.
- They travel into the feature-document at ship (distilled from the fc).
- `tdd` instruments them: each metric maps to an event/counter the running app emits; a bdd acceptance row can pin "emits metric X" so instrumentation ships with the feature.
- `ship-in-prd`'s release flag tags the cohort, so values are attributable to the launched cohort vs baseline.
- `measure` reads the cohort's values from the telemetry sink [PROJECT: dashboard / query / metrics store] and compares to the threshold from the fc.

Defined in ①, instrumented in ③, released to a cohort in ④, judged in ④.

## 2. Writing principles: DRY, KISS, YAGNI

These apply to every text surface, for AI agents and humans alike: buildloop docs, code comments, test names, commit messages, PR bodies, and agent self-checks.

- **DRY**: concise sentences. No redundancy, no over-explaining.
- **KISS**: simple solutions. Small, targeted changes that are easy to review.
- **YAGNI**: build only what today's task asks for. Preserve existing behavior unless the task is a behavior change. No broad refactors during localized fixes.

The think-gate and plan-gate enforce concrete style checks in their rubrics. Humans judge the rest.

## 3. Skills and agents

### 3.1 Namespace

BuildLoop skills use the `buildloop:` namespace: `/buildloop:interview-me`, `make-prototypes`, `does-it-worth`, `build-plan`, `ddd`, `bdd`, `tdd`, `build-gate`, `ship-in-prd`, `measure`, `tweak-it`, `log`.

Agents: `think-gate`, `plan-gate`, `code-checker`, plus the project-provided `ux-checker`.

Built-ins couple in directly (Claude-only for now, YAGNI; add an AI-agnostic layer if a second target ever lands): `/simplify`, `/code-review`, `/security-review`, `/verify`, and the `plan` agent.

[PROJECT: add your own gate agents under `.claude/agents/` for the slots your stack needs, such as a project `security-reviewer`, and name them here.]
