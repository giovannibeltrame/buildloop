<!--
BuildLoop operating manual — TEMPLATE.

Drop this file at your repository root as `AGENTS.md` and adapt the bracketed
[PROJECT: …] notes to your stack. The buildloop plugin's skills and agents
reference this file by section number (e.g. "AGENTS.md §6.2"), so keep the
section numbering intact when you edit. Delete a section's content only if you
also remove the rule it encodes everywhere it is referenced.
-->

# Agent Instructions

This file is the canonical operating manual for this project. It is tool-agnostic — every agent, skill, and human contributor reads from here. `CLAUDE.md` at the root is a thin pointer to this file plus a small set of non-negotiables that must be unconditionally loaded.

## 1. Where rules live

Three-layer convention for every rule in this file:

> **Declarative text lives in AGENTS.md. Operational enforcement lives in skills. Auditing lives in agents.**

- Change a rule → edit AGENTS.md.
- Change enforcement → edit a buildloop skill.
- Change auditing → edit an agent under `.claude/agents/` (or the plugin's bundled agents).

Each cross-cutting rule below is the single source of truth. Skills and agents that enforce or audit a rule **reference it by name** rather than restating it.

## 2. Product Alignment

Before product, architecture, analytics, UI, or wording changes, check your project's product docs and keep changes aligned with them.

[PROJECT: list your product docs and the product rules every change must honor, e.g.

- `docs/about-us/vision.md`, `docs/about-us/principles.md`, `docs/about-us/architecture.md`
- one-line product rules: what the product is and is not, naming discipline, the bar you hold (correctness / explainability / stability) before scale.

`/buildloop:ddd-refine` reads the product docs a doc touches via this section, so name them here.]

## 3. The build loop

### 3.1 Status flow

```
Triage (implicit)
   │
   ├── Not a dev request → no doc
   ├── Empirical claim → hyp-NNNN.md (its own short flow)
   └── Build / fix / change → main flow:
                   1. To be refined
                   2. Refining  ⇄  2-Blocked
                   3. Ready for backlog       (gate)
                   4. Planned
                   5. In progress
                   6. In review (automated)   (gate)
                   7. UAT                     (gate)
                   8. Completed / Dropped
```

**Bounces:**
- 6 → 5 if any automated gate flags a real implementation issue.
- 7 → 5 if UAT shows wrong implementation, right scenario.
- 7 → 2 if UAT shows the scenario itself was wrong (mis-modeled WHAT).

### 3.2 Process table

| # | Status | Entry trigger | Owner / mechanism | Exit condition | Mode |
|---|---|---|---|---|---|
| 0 | Triage | User message implies build / fix / change intent | Claude suggests `/buildloop:create-doc`; user confirms | Triage decision made | Hybrid |
| 1 | To be refined | User confirms / "create a doc" | `/buildloop:create-doc` triages between epic / bugfix / hypothesis / constant | New `<kind>-NNNN.md` exists | Manual |
| 1-Hotfix | Hotfix exception | Production-affecting urgent bug needing immediate fix | User implements with `[HOTFIX]` commits per §5.1; follow-up `fix-NNNN.md` filed within 24h | Bugfix doc exists + linked from hotfix commits | Manual |
| 2 | Refining | User: "let's refine" | `/buildloop:ddd-refine` | Skill declares no open questions | Manual |
| 2-Blocked | Blocked sub-state | Refiner flags open business question | Skill writes question into doc | User answers question | Auto (skill) |
| 2-Blocked → 2 | Resume | User answers + re-invokes refine | User → skill | Answer captured in doc | Manual re-invoke |
| 3 | Ready for backlog | Refine declares "no open questions" → spawns the gate | Read-only gate (§6.2) | All gate criteria pass (§6.2) | Auto gate |
| 4 | Planned | User orders priority in `roadmap.md` | User (may consult Claude) | Item in roadmap | Manual |
| 5 | In progress | User: "implement X" / "next on roadmap" | `/buildloop:implement` orchestrates `test-writer` (RED) → impl (GREEN) → refactor; `/simplify` + `/code-review` mandatory before exit | TDD closed for all scenarios, all BDD green, quality skills ran | Manual (skill) |
| 5 → 6 | Hand to gates | `/implement` declares ready | Skill triggers gate agents | All gates dispatched | Auto |
| 6 | In review (auto) | Gates run per own triggers | `code-checker` + project-provided gates (§3.5) | All gates green | Auto |
| 6 → 5 | Bounce | Any agent flags real impl issue | Failing agent emits findings | — | Auto |
| 6 → 7 | Promote | All gates green | — | — | Auto |
| 7 | UAT | `/verify` drives running app + human stakeholder | `/verify` skill + human | BDD scenarios validated in real system | Hybrid |
| 7 → 5 | Bounce (impl wrong) | UAT shows code wrong | User signal → skill | — | Manual |
| 7 → 2 | Bounce (scenario wrong) | UAT shows scenario mis-modeled WHAT | User signal → skill | — | Manual |
| 8 | Completed | UAT pass + PR merged | User merges | Doc marked done, process log closed | Manual |
| 8 | Dropped | User decides stop | User + `/buildloop:log` writes one-line reason | Doc archived with reason | Manual |

Only purely-human transitions are **3 → 4** (prioritization) and the terminal flips (merge, drop). Everything else has a mechanical owner.

### 3.3 Doc kinds

| Kind | Path | Purpose | Flow |
|---|---|---|---|
| Epic | `docs/buildloop/epics/epic-NNNN.md` | Features, improvements, audits | Main flow |
| Bugfix | `docs/buildloop/bugfixes/fix-NNNN.md` | Bugs | Main flow |
| Hypothesis | `docs/buildloop/hypotheses/hyp-NNNN.md` | Empirical claims | Open → Investigating → Validated (spawns epic) / Invalidated (archived with one-line learning; MAY trigger follow-up doc) |
| Constant | `docs/buildloop/constants/cnst-NNNN.md` | Platform truths the system must uphold | Main flow with derived labels: Proposed (statuses 1–7) → Active (status 8 Completed) → Deprecated → Superseded |

**Constant definition**: a constant is a behavior the system must follow. It continues to hold despite new epics, audits, bugfixes, and improvements — only an explicitly intentional, justified change overrides this. Other work cannot break a constant accidentally; if a new epic's scope would break a constant, the conflict must be reconciled (epic redesigned) or the constant change must be in the epic's explicit deliverables.

**Constant doc shape**: headline, rationale, evidence (e2e tests + paths), change log (each revision with reason + hypothesis link).

**Constant proposal**: constants are proposed via `/buildloop:create-doc` (triage recognizes "platform truth" intent). The constant then traverses the same main flow as an epic. The 2 → 3 gate applies; criterion 3 (WHAT we must build) for a constant means the e2e tests that enforce it.

**Invalidated hypothesis follow-up**: an invalidated hypothesis stays archived with its one-line learning. The invalidation MAY trigger a follow-up doc (epic, bugfix, or another hypothesis) capturing what the invalidation implies. Follow-up doc MUST link back to the invalidating hypothesis. The hypothesis itself never reopens.

**Umbrella epics**: permitted but only as umbrellas. Every sibling mentioned in the umbrella body MUST be linked by ID to an existing doc. Each sibling MUST link back to its umbrella. The 2 → 3 gate rejects orphan references in either direction.

### 3.4 Auto-suggest at triage

When a user message describes building, fixing, or changing something, Claude proposes `/buildloop:create-doc` before writing code. User confirms or redirects. No auto-firing — the explicit `/buildloop:create-doc` invocation is always available as a manual entry. Questions, exploration, and discussion do not trigger the suggestion.

### 3.5 Quality gates mapping

| Moment | Tool | Why |
|---|---|---|
| During status 5 | `/simplify`, `/code-review` | Ongoing quality in main thread; **mandatory before 5 → 6** |
| 5 → 6 | `code-checker`, plus any project-provided gate agents (see below) | Project-specific gates with project context |
| 6 → 7 | `/verify` | Drives the running app to confirm BDD scenarios hold in reality |

**Project-provided gate agents.** The plugin ships the generic `code-checker`, `doc-validator` and `test-writer` agents. Add your own gate agents under `.claude/agents/` for the slots your stack needs, and list them here. Two common ones:

- `security-reviewer` — runs at 5 → 6 when the diff touches sensitive paths (auth, secrets, external feeds, data stores, deploy config). [PROJECT: add and name yours, or delete this row.]
- `ux-checker` — runs at 5 → 6 when the diff touches UI; see §4.9. [PROJECT: add and name yours, or delete this row.]

## 4. Cross-cutting rules

### 4.1 BDD scenario format

```
Scenario [unit | integration | e2e]: <one-line behavior name>
  Given <single precondition>
  When <single action>
  Then <single observable outcome>
```

- No `And`, no `But`. One Given, one When, one Then.
- One behavior per scenario; five behaviors → five scenarios.
- Layer tag is mandatory: epic and bugfix scenarios use `[unit]` or `[integration]`; constant scenarios are always `[e2e]`.
- Strict format, no parser today — preserves optionality to plug in a BDD runner later without rewriting docs.
- `/buildloop:ddd-refine` raises the layer question per scenario during refinement (does this behavior cross component boundaries?).
- The 2 → 3 gate rejects any scenario missing a layer tag or violating the And/But / one-of-each rule.

### 4.2 Test layers

| Layer | Source doc | Lives in | Written during | Owned by |
|---|---|---|---|---|
| Unit | Epic / bugfix scenarios tagged `[unit]` | `tests/unit/<area>/` | Status 5 TDD loop | `test-writer` + implementer |
| Integration | Epic / bugfix scenarios tagged `[integration]` | `tests/integration/` | Status 5, after unit | `test-writer` + implementer |
| E2E | Constant scenarios (all e2e by definition) | `tests/e2e/` | When the constant is implemented (main flow status 5) | `code-checker` audits coverage; `/verify` exercises in running app |

`code-checker` audits: every scenario has a corresponding test file in the layer matching its tag. [PROJECT: adjust the test-directory paths to your layout if they differ.]

### 4.3 Test naming convention

Mandatory across all layers. The class / suite name is a direct transliteration of the doc's `Given / When` clauses; the methods / cases correspond to the `Then`s — one assertion each. This gives grep-traceability between doc scenarios and code.

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

### 4.4 100% coverage + non-product files list

**Rule**: 100% line coverage on all source files. Audited by `code-checker` at the 5 → 6 gate.

**Exclusions** — the **non-product files list**, single source of truth, reused below for the doc-less commit exception. [PROJECT: maintain this list for your repo. Typical entries:]

- config / manifest files (`.env*`, `*.cfg`, `pyproject.toml`, `package.json`)
- `.claude/settings.json`, `.claude/launch.json`
- deploy / ops config
- generated files (type stubs, build artifacts)
- thin CLI shims

**Thin CLI shim** = a file whose top-level code is only argument parsing + a single delegating call to library code. If logic creeps in, it crosses the line. Judgment call audited by `code-checker`.

### 4.5 TDD red-first

Non-negotiable.

1. Write the failing test. Run it. **Observe RED.** Failing for the right reason — not setup or import error.
2. Write the minimum code to flip GREEN. Nothing more.
3. Refactor with all prior tests still GREEN.
4. Loop.

Additional locks:

- **One test at a time.** The red→green→refactor cycle is per individual test, not per scenario, not per file. Writing five tests then writing code that flips them all green at once violates TDD even if the end-state coverage is identical.
- **One BDD scenario typically unfolds into N TDD tests.** A scenario describes business-level behavior; implementation has multiple correctness conditions, each deserving its own assertion. Each test still goes through red→green individually.
- **Scenarios are tackled sequentially.** Scenario 1's tests done, then scenario 2.
- Tests never seen RED don't count.

**Exemption — characterization testing of unchanged code.** A pure coverage-backfill fix that adds tests to *existing, unmodified* production code cannot observe a meaningful RED — the behavior already works, so each test is GREEN on first run. Such a fix is **characterization testing, not TDD**, and is exempt from the per-test red-first loop above. It applies only when **no production code changes**; the moment the fix alters behavior, red-first applies in full. A characterization fix must instead:

1. **Declare it** in the doc's process log (so the absence of `RED —` commits is expected, not a lapse).
2. **Drive the real red→green at the coverage-gate level** — the file moves from `<100%` to `100%`.
3. **Prove each test bites via a mutation spot-check** — temporarily break the line under test (or flip its assertion), confirm the test fails, then revert — so 100% coverage isn't satisfied by tautological assertions that would never catch a regression.

`/buildloop:implement` enforces operationally (sequences commits with RED / GREEN tags per §5.1). `code-checker` audits for RED → GREEN evidence in commit history and the process log — or, for a process-log-declared characterization fix, for the coverage-gate red→green plus a recorded mutation spot-check.

### 4.6 DDD anti-assumption (Status 2 guardian)

`/buildloop:ddd-refine` is the guardian of the WHAT.

- Every ambiguity becomes a question, never a guess.
- "I think the user probably means…" is forbidden — ask instead.
- The skill MUST NOT propose 2 → 3 while any open question exists; the doc moves to 2-Blocked instead.
- "Implicit requirements" don't exist in this skill's vocabulary. If it's not in the doc, it's not agreed.

### 4.7 Writing principles — DRY, KISS, YAGNI

Three principles apply to every text surface in this project — AI agents and humans alike, in buildloop docs, code comments, test names, commit messages, PR bodies, and agent self-checks:

- **DRY** — write concise sentences. No redundancy, no over-explaining.
- **KISS** — simple solutions. Small, targeted changes that are easy to review.
- **YAGNI** — build only what today's task asks for. Preserve existing behavior unless the task is explicitly a behavior change. No broad refactors during localized fixes.

The 2 → 3 gate enforces concrete style checks; see §6.2 criteria 1 and 9. Humans judge the rest.

### 4.8 Process log

Every doc has a `## Process log` section at the bottom that grows over time. Each entry is one line; tagged `[auto]` or `[human, manual]`.

**At transition time**: the skill or agent that owns the transition appends a log line to the doc immediately. Live state.

**At commit time**: the commit footer references the log entry per §5.1. Durable audit trail in git history.

**Enforcement on human-owned steps** (3 → 4, terminal flips, 7 → 5/2 manual signals) — three layers:

1. **Auto-suggest by Claude** in the moment, watching for human-step language ("I prioritized…", "let's drop epic-X", "I approved the UAT").
2. **Pre-commit hook** (optional, deterministic backstop) — refuses commits that change a doc's status field without a matching new `## Process log` entry.
3. **Gate audit** — `code-checker` checks at 5 → 6 that every recent status transition has a matching log entry. Flags drift.

Manual entries written via the `/buildloop:log` helper.

### 4.9 UX validation routing

UX validation is performed by a project-provided `ux-checker` agent (if your project has a UI), per its own routing rules — typically production-read mode for UI-only diffs, local-stack mode when the diff touches API, DB, or schema. This rule lives in that agent's definition, not duplicated here. [PROJECT: add a `ux-checker` agent under `.claude/agents/` if you have user-facing surfaces; otherwise this section is inert.]

## 5. Commits, PRs, sequence numbers

### 5.1 Commit message format

```
(<doc-id>): <short imperative description>
```

- ≤ 70 chars on the header line.
- `<doc-id>` ∈ `epic-NNNN` / `fix-NNNN` / `hyp-NNNN` / `cnst-NNNN`.
- Body optional (1–3 sentences on *why*).
- **Footer required on status transitions**:
  ```
  Process-log: <doc-id> status <X> → <Y>
  ```
- **TDD cycle commits** inside status 5 carry explicit tags in the description:
  ```
  (epic-0024): RED — snapshot row insertion fails for missing event
  (epic-0024): GREEN — write snapshot row for missing event
  (epic-0024): refactor — extract snapshot writer helper
  ```
  `code-checker` audits TDD discipline by greping for unpaired RED entries.

**Multi-doc commits**: `(epic-0024,epic-0018): ...` — allowed but discouraged; prefer splitting one commit per doc.

**Doc-less commit exceptions**:

- Commits touching only files in the **non-product files list** (§4.4) — same list, reused.
- Commits touching only README or non-buildloop docs.
- `[HOTFIX]`-tagged commits (header prefix) — require a follow-up `fix-NNNN.md` filed within 24 hours; `code-checker` audits the follow-up exists.

### 5.2 PR template

Lives at `.github/PULL_REQUEST_TEMPLATE.md`. GitHub renders it automatically on new PRs. Body:

```markdown
## Doc
- Primary: [<doc-id>](docs/buildloop/<kind>/<doc-id>.md)
- Related: [<doc-id>](...), [<doc-id>](...)

## Summary
<one paragraph: what changes from the user/system perspective — the headline restated, plus any nuance>

## BDD scenarios delivered
- <scenario name 1>
- <scenario name 2>

## Process log entry
Status transition: <X> → <Y>  (or "no transition; mid-status work")
See `<doc-id>.md` § Process log → newest entry.

## Quality gates
- [ ] `/simplify` clean
- [ ] `/code-review` clean
- [ ] `code-checker` clean
- [ ] project gate agents clean (or N/A — diff doesn't touch their paths)
- [ ] `/verify` UAT pass (or pending — PR opens at 5 → 6, UAT happens at 6 → 7)

## Test plan for reviewer
- [ ] <thing for human reviewer to verify>
```

**Lifecycle**: the PR is opened at the 5 → 6 transition (handing off to automated review) with the UAT box unchecked. UAT happens on the PR branch. UAT pass + all boxes checked = merge = status 8 Completed. The PR spans statuses 6 and 7; merge is the 7 → 8 transition.

**Hotfix PRs**: `[HOTFIX]` prefix in PR title; follow-up bugfix doc ID committed in the same PR or referenced as a follow-up issue.

### 5.3 Sequence number scheme

`NNNN` = 4 digits, zero-padded. Per kind (epic-0001…, fix-0001…, hyp-0001…, cnst-0001…). Sufficient for now. Expanding to 5 digits is a clean future change if any kind approaches 9999.

## 6. Skills and agents

### 6.1 Skill namespace

All buildloop skills use the `buildloop:` namespace (provided by the BuildLoop plugin). The family: `/buildloop:create-doc`, `/buildloop:ddd-refine`, `/buildloop:implement`, `/buildloop:log`, `/buildloop:audit`, `/buildloop:promote`.

Built-in skills used at specific moments are not renamespaced: `/simplify`, `/code-review`, `/verify`.

### 6.2 Ready-for-backlog gate criteria (2 → 3)

A doc passes the 2 → 3 gate only when all of the following are present and clean:

1. **Single headline** sentence at top, no "and".
2. **WHAT problem we must solve** — problem statement.
3. **WHAT we must build** — architecture, models, domains, rules, services, code per DDD.
4. **HOW system must behave** — acceptance scenarios in strict BDD format (no And/But, one Given/When/Then each, mandatory layer tag `[unit | integration | e2e]`).
5. **No open questions / no unresolved blocking decisions.**
6. **Umbrella links bidirectional** (when applicable — siblings linked, parent linked back).
7. **Prototype present** (when UX epic — wireframe minimum).
8. **Constants conflict check** — if the doc's scope would break a `cnst-NNNN.md`, the doc MUST explicitly include "supersedes cnst-NNNN" in deliverables, OR be redesigned to not break it.
9. **No preamble, no echo, no restatement** — no prose paragraph between H1 and the first `##` (structured one-line metadata like `Status:`, `Headline:`, `Umbrella:` is allowed); no section whose first sentence repeats the section title; no restatement of a rule already defined elsewhere in AGENTS.md.

Returns pass/fail + concrete gap list per failed criterion.
