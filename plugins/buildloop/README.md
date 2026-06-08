# buildloop

A Claude Code plugin that operationalizes a small, opinionated development loop:

> **(Re)Think it → Plan it → Build it → Ship it. Loop again.**

Four phases, three gates between them, and a log table — not a status field — as the single source of truth for where each piece of work is. It bakes in BDD scenarios, DDD anti-assumption discipline, red-first TDD, and a quality gate that mixes automated checks with human sign-off.

It ships three layers:

- **Declarative** — an `AGENTS.md` operating manual (provided as a template) holding the cross-cutting rules: project context, the phase/loop map, and the gate contracts.
- **Enforcement** — twelve skills that drive the four phases (each owns its method rule: BDD → bdd, TDD → tdd, DDD → ddd, …).
- **Auditing** — four review agents (each gate agent owns its pass-criteria).

## What's inside

```
skills/                       ── the four phases ──
  ① (Re)Think it
    interview-me     — create a feature candidate (fc): WHY · narrative · hypotheses · metrics
    make-prototypes  — lo-fi UX options in the fc
    does-it-worth    — verdict (yes / not yet / park / never); on "yes" runs the think-gate
  ② Plan it
    build-plan       — orchestrate ddd + bdd + the plan agent into a build plan (bp); full or lite
    ddd              — WHAT: domain models, rules, services + Ubiquitous Language
    bdd              — HOW: acceptance scenarios as a strict one-behavior-per-row table
  ③ Build it
    tdd              — red → green → refactor, owning the failing test; tagged commits
    build-gate       — orchestrate the 8-step gate sequence + the three-way bounce
  ④ Ship it
    ship-in-prd      — distill fc+bp → living feature-document + CHANGELOG; deploy ≠ release
    measure          — judge the cohort's live metrics vs the fc thresholds; rollout verdict
    tweak-it         — route a change back into the loop: lite→② / full→①
  (any phase)
    log              — single writer of the ## Log table; appends a work or transition row
agents/
  think-gate    — read-only ① → ② gate; validates the fc against its own criteria
  plan-gate     — read-only ② → ③ gate; validates the bp against its own criteria, folds in a simplify pass
  code-checker  — build-gate clarity / complexity / coverage-classification / TDD-discipline audit
  ux-checker    — build-gate UX gate (optional, UI diffs); self-routing, read-only
templates/
  AGENTS.md           — the operating manual to drop at your repo root and adapt
  fc-xxxx.md          — feature candidate (working doc)
  bp-xxxx.md          — build plan (working doc)
  feature-document.md — living doc, one per feature
  CHANGELOG.md        — repo-root change history
bin/
  buildloop     — zero-dependency helper: next-id, doc-id, current-phase, open-questions, log
```

Built-in Claude skills couple in directly at the build-gate (`/simplify`, `/code-review`, `/security-review`, `/verify`) along with the `plan` agent — Claude-only for now (YAGNI).

`bin/buildloop` is added to the Bash tool's `PATH` automatically while the plugin is enabled, so the skills call a bare `buildloop …` — no path interpolation, no `python -m`. It only needs `python3` on the machine. The current phase of any doc is derived from its `## Log` table (`buildloop current-phase <doc>`), never a stored status.

## Adopting it in a project

1. **Install the plugin** (see the repo root [README](../../README.md) for marketplace setup):
   ```
   /plugin marketplace add giovannibeltrame/buildloop
   /plugin install buildloop@buildloop
   ```
2. **Drop the operating manual in.** Copy `templates/AGENTS.md` to your repo root as `AGENTS.md` and work through the `[PROJECT: …]` notes — product docs, test commands, test-framework conventions, the non-product files list, the telemetry sink, and the deploy/flag mechanism. **Keep the section numbering intact** — the skills and agents reference rules by number (e.g. `AGENTS.md §2.4`).
3. **Create the docs tree** the skills expect:
   ```
   docs/buildloop/{working,working/archive,living}/
   ```
   (`CHANGELOG.md` lives at the repo root.)
4. **Add your project-specific gate agents** (optional). The plugin ships the generic `code-checker`, `think-gate`, `plan-gate`, and `ux-checker`. If your stack needs more (e.g. a `security-reviewer`), add it under `.claude/agents/` and name it in `AGENTS.md §5.1`.

## The loop at a glance

```
① (Re)Think it ─[think-gate]→ ② Plan it ─[plan-gate]→ ③ Build it ─[build-gate]→ ④ Ship it
        ↑                                                   │ fail · premise → ①
        │ tweak full / invalidated                          │ fail · scenario → ②
        └───────────────── ④ ──────────── tweak lite → ② ───┘ fail · impl → ③
```

A new idea enters at ①; a change to a shipped feature enters via `tweak-it`. Gates check *state* (artifacts present + valid), never *history*, so every skill and agent also works standalone. See `AGENTS.md` for the full contracts.
