<!--
BuildLoop operating manual — TEMPLATE.

Drop this file at your repository root as `AGENTS.md` and adapt the bracketed
[PROJECT: …] notes to your stack. The buildloop plugin's skills and agents
reference this file by section number (e.g. "AGENTS.md §2.4"), so keep the
section numbering intact when you edit. Delete a section's content only if you
also remove the rule it encodes everywhere it is referenced.

Scope: this file holds only what is cross-cutting — project context, the phase/
loop map, and the gate contracts. Method rules live in their owning skill
(BDD → bdd, TDD → tdd, DDD → ddd, the build-gate sequence → build-gate, deploy
→ ship-in-prd) and gate pass-criteria live in their gate agent (think-gate,
plan-gate). This file points at those owners; it does not restate them.
-->

# Agent Instructions

This file is the canonical operating manual for this project. It is tool-agnostic — every agent, skill, and human contributor reads from here. `CLAUDE.md` at the root is a thin pointer to this file plus the few non-negotiables that must always load.

## 1. Product alignment

Before product, architecture, analytics, UI, or wording changes, check your project's product docs and keep changes aligned with them.

[PROJECT: list your product docs and the product rules every change must honor, e.g.

- `docs/about-us/vision.md`, `docs/about-us/principles.md`, `docs/about-us/architecture.md`
- one-line product rules: what the product is and is not, naming discipline, the bar you hold (correctness / explainability / stability) before scale.

`interview-me` and `ddd` read the product docs a candidate touches via this section, so name them here.]

## 2. The build loop

(Re)Think it → Plan it → Build it → Ship it. Loop again.

### 2.1 Phases and gates

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

### 2.2 State lives in the log table

There is no `Status:` field. Each doc carries a `## Log` table that is the single source of truth for "where are we."

- **Fixed four columns**: `when | who | phase → | what`.
- **Two row kinds.** A *work* row is intra-phase progress (`phase →` is `—`). A *transition* row carries `<From> → <To>`. **Current phase = the To-side of the most recent transition row.**
- **Append-only, single writer.** Only the `log` skill writes the table, newest at the bottom — which is what makes "last transition = state" trustworthy. The append mechanics live in that skill.
- **No top-of-doc cache.** Derive the phase from the log; `buildloop current-phase <doc>` does this deterministically.
- **Canonical phase tokens**: `① (Re)Think it · ② Plan it · ③ Build it · ④ Ship it`.

### 2.3 The phases

Each phase names the skills that run in it and the artifact it produces. Skills carry their own method rules; this section is the WHAT and WHY.

| Phase | Skills | Produces |
|---|---|---|
| **① (Re)Think it** | `interview-me` (WHY · narrative · hypotheses · metrics) → `make-prototypes` (lo-fi) → `does-it-worth` (verdict) | `fc-NNNN.md` |
| **② Plan it** | `build-plan` orchestrates `ddd` (WHAT) + `bdd` (HOW) + the `plan` agent (decomposition) | `bp-NNNN.md` |
| **③ Build it** | `tdd` (red → green → refactor) → `build-gate` | tests + code |
| **④ Ship it** | `ship-in-prd` (deploy + release) → `measure` (hypothesis verdict) → `tweak-it` (bugfix / improvement) | `feature-document.md` + `CHANGELOG.md` |

### 2.4 Gates

A gate is a **read-only verdict function of doc state** — never of which skill ran. Because it depends only on artifacts-present-and-valid and never writes, it works identically whether spawned by the advancing skill or invoked standalone (`/<gate> <doc>`); on pass, the write of the transition row is always the separate step owned by whoever advances.

**Already-past guard**: a gate first reads `buildloop current-phase`; if the doc is already past its phase, it returns a one-line "already at phase X" and skips the rubric.

| Gate | Kind | Validates | Where the rule lives | Emits |
|---|---|---|---|---|
| `think-gate` | agent (read-only) | the fc is complete | `think-gate` agent | `① → ②` |
| `plan-gate` | agent (read-only) | the bp is valid (DDD + BDD + decomposition) | `plan-gate` agent | `② → ③` |
| `build-gate` | skill (orchestrator) | the 8-step sequence is clean | `build-gate` skill | `③ → ④`, else bounce |

**Build-gate bounce** routes a failure to the phase that owns the defect: implementation wrong → ③ (`tdd`); scenario mis-modeled → ② (`build-plan`); premise wrong → ① (`interview-me`).

### 2.5 Doc kinds and lifecycle

| Doc | Kind | Path | Role |
|---|---|---|---|
| `fc-NNNN.md` | feature candidate | `docs/buildloop/working/` | WHY · narrative · hypotheses · metrics · prototypes · verdict |
| `bp-NNNN.md` | build plan | `docs/buildloop/working/` | phases · WHAT/DDD · HOW/BDD · decomposition |
| `feature-document.md` | living doc | `docs/buildloop/living/` | one per feature; hypotheses + metrics + invariants + release |
| `CHANGELOG.md` | change history | repo root | WHO changed WHAT, newest first |

- **fc and bp are working (transient) docs.** They hold the full log until ship, then archive under `docs/buildloop/working/archive/`.
- **feature-document.md is the living doc.** `ship-in-prd` distills the fc + bp hypotheses, metrics, and invariants into it, so `measure` has something to read. It keeps only its **last transition**; change history goes to `CHANGELOG.md`.
- **No constants doc.** Invariants the system must uphold are declared in a feature-document's **"Invariants (e2e-guarded)"** section and guarded by e2e tests.

### 2.6 Skill contracts

Each skill declares **Requires (state) / Produces / Standalone fallback**. *Requires* is a precondition on state (artifact present + phase read from the log), never "skill X ran first." Skills sequence themselves because one skill's Requires is another's Produces; neither names the other.

| Skill | Requires (state) | Produces | Standalone fallback |
|---|---|---|---|
| `interview-me` | — (entry); or an existing fc to re-sharpen | fc (WHY · narrative · hypotheses · metrics) | works anywhere; creates a new fc |
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

**Two Requires that nothing else Produces — external inputs, by design:**
- `measure` needs **live prod data** — from telemetry on the shipped cohort, not any doc (§3.5).
- `ship-in-prd` needs the **deploy + feature-flag** mechanism — infra, not a skill output (owned by the `ship-in-prd` skill).

Everything else chains: an upstream Produces satisfies each Requires.

### 2.7 Auto-suggest at triage

When a user message describes building, fixing, or changing something, Claude proposes the entry skill before writing code — `interview-me` for a new idea, `tweak-it` for a change to a shipped feature. User confirms or redirects. No auto-firing; the explicit skill invocation is always available. Questions, exploration, and discussion do not trigger the suggestion.

## 3. Project conventions

Cross-cutting rules with project-specific content. Method rules (BDD/TDD/DDD) are not here — they live in their skills.

### 3.1 Test layers

| Layer | Source | Lives in | Written during | Audited by |
|---|---|---|---|---|
| Unit | bp scenarios tagged `[unit]` | `tests/unit/<area>/` | ③ `tdd` loop | `code-checker` |
| Integration | bp scenarios tagged `[integration]` | `tests/integration/` | ③, after unit | `code-checker` |
| E2E | feature-document invariants (always e2e) | `tests/e2e/` | when the invariant ships | `code-checker` coverage; `/verify` in the running app |

`code-checker` audits that every scenario has a test file in the layer matching its tag. [PROJECT: adjust test-directory paths to your layout if they differ.]

### 3.2 Test naming convention

Mandatory across all layers. The class / suite name transliterates the scenario's `Given / When`; the methods / cases correspond to the `Then`s — one assertion each. This gives grep-traceability between doc scenarios and code.

[PROJECT: pin the exact convention for your test framework(s). Examples:

**Python (pytest, class-based):**
```python
class TestSubject_WhenCondition:
    def test_should_expected_behavior(self): ...
```

**Node (node:test, describe/it):**
```js
describe('Subject when condition', () => {
  it('should expected behavior', () => { ... })
})
```
]

### 3.3 100% coverage + non-product files list

**Rule**: 100% line coverage on all source files. Audited by `code-checker` at the build-gate.

**Exclusions** — the **non-product files list**, single source of truth, reused for the doc-less commit exception (§4.1). [PROJECT: maintain this list for your repo. Typical entries:]

- config / manifest files (`.env*`, `*.cfg`, `pyproject.toml`, `package.json`)
- `.claude/settings.json`, `.claude/launch.json`
- deploy / ops config
- generated files (type stubs, build artifacts)
- thin CLI shims

**Thin CLI shim** = a file whose top-level code is only argument parsing + a single delegating call to library code. If logic creeps in, it crosses the line. Judgment call audited by `code-checker`.

### 3.4 Writing principles — DRY, KISS, YAGNI

Apply to every text surface — AI agents and humans alike, in buildloop docs, code comments, test names, commit messages, PR bodies, and agent self-checks:

- **DRY** — concise sentences. No redundancy, no over-explaining.
- **KISS** — simple solutions. Small, targeted changes that are easy to review.
- **YAGNI** — build only what today's task asks for. Preserve existing behavior unless the task is explicitly a behavior change. No broad refactors during localized fixes.

The think-gate and plan-gate enforce concrete style checks in their rubrics. Humans judge the rest.

### 3.5 Telemetry seam

`measure` needs live prod data, which no skill produces. The chain that supplies it:

- Metrics + success threshold are defined in `interview-me` (fc) — the "which metrics tell us success" question.
- They travel into the feature-document at ship (distilled from the fc).
- `tdd` instruments them: each metric maps to an event/counter the running app emits; a bdd acceptance row can pin "emits metric X" so instrumentation ships with the feature.
- `ship-in-prd`'s release flag tags the cohort, so values are attributable to the launched cohort vs baseline.
- `measure` reads the cohort's values from the telemetry sink [PROJECT: dashboard / query / metrics store] and compares to the threshold from the fc.

This closes the hypothesis loop: defined in ①, instrumented in ③, released to a cohort in ④, judged in ④.

## 4. Commits, PRs, sequence numbers

### 4.1 Commit message format

```
(<doc-id>): <short imperative description>
```

- ≤ 70 chars on the header line.
- `<doc-id>` ∈ `fc-NNNN` / `bp-NNNN`.
- Body optional (1–3 sentences on *why*).
- **Footer required on phase transitions**:
  ```
  Log: <doc-id> <From> → <To>
  ```
- **TDD cycle commits** inside phase ③ carry explicit tags in the description (`RED —` / `GREEN —` / `refactor —`); `code-checker` audits the discipline. The tag rule lives in the `tdd` skill.

**Doc-less commit exceptions**:
- Commits touching only files in the **non-product files list** (§3.3).
- Commits touching only README or non-buildloop docs.

### 4.2 PR template

Lives at `.github/PULL_REQUEST_TEMPLATE.md`. Body:

```markdown
## Doc
- Plan: [bp-NNNN](docs/buildloop/working/bp-NNNN.md)
- Candidate: [fc-NNNN](docs/buildloop/working/fc-NNNN.md)

## Summary
<one paragraph: what changes from the user/system perspective>

## BDD scenarios delivered
- <scenario name 1>
- <scenario name 2>

## Log entry
Transition: <From> → <To>  (or "no transition; intra-phase work")
See `bp-NNNN.md` § Log → newest row.

## Quality gates
- [ ] `/simplify` clean
- [ ] `code-checker` clean
- [ ] `/code-review` clean
- [ ] `/security-review` clean
- [ ] `ux-checker` clean (or N/A — diff doesn't touch UI)
- [ ] `/verify` UAT pass (or pending — PR opens at build-gate, UAT happens before ④)

## Test plan for reviewer
- [ ] <thing for the human reviewer to verify>
```

**Lifecycle**: the PR opens when `build-gate` starts (handing off to automated review) with the UAT box unchecked. UAT happens on the PR branch. UAT pass + all boxes checked = merge = phase ④.

### 4.3 Sequence number scheme

`NNNN` = 4 digits, zero-padded, per kind (`fc-0001…`, `bp-0001…`). `buildloop next-id <fc|bp>` issues the next, reserving archived numbers. Expanding to 5 digits is a clean future change if a kind approaches 9999.

## 5. Skills and agents

### 5.1 Namespace

BuildLoop skills use the `buildloop:` namespace: `/buildloop:interview-me`, `make-prototypes`, `does-it-worth`, `build-plan`, `ddd`, `bdd`, `tdd`, `build-gate`, `ship-in-prd`, `measure`, `tweak-it`, `log`.

Agents: `think-gate`, `plan-gate`, `code-checker`, plus the project-provided `ux-checker`.

Built-ins couple in directly (Claude-only for now — YAGNI; add an AI-agnostic layer if a second target ever lands): `/simplify`, `/code-review`, `/security-review`, `/verify`, and the `plan` agent.

[PROJECT: add your own gate agents under `.claude/agents/` for the slots your stack needs — e.g. a project `security-reviewer` — and name them here.]
