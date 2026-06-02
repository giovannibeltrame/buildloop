# buildloop

A Claude Code plugin that operationalizes a small, opinionated software development workflow: **Discover → Refine → Code → Test → Implement → Loop**, with BDD scenarios, DDD anti-assumption refinement, red-first TDD, and quality gates.

It ships three layers (per `AGENTS.md §1`):

- **Declarative** — an `AGENTS.md` operating manual (provided as a template) that is the single source of truth for every rule.
- **Enforcement** — six skills that drive the flow.
- **Auditing** — three review agents.

## What's inside

```
skills/
  create-doc    — triage intent → next-sequence doc (epic/fix/hyp/cnst) at status 1
  ddd-refine    — status-2 guardian: one question per ambiguity, strict BDD, routes to the 2→3 gate
  implement     — status-5 TDD orchestrator: red → green → refactor, then mandatory quality gates
  log           — append a process-log line + the matching commit footer
  audit         — sweep for drift, file a linked sibling per gap
  promote       — flip a constant Proposed → Active once its e2e test exists
agents/
  code-checker  — clarity / complexity / coverage-classification gate (5→6)
  doc-validator — read-only 2→3 "ready for backlog" gate (9 criteria)
  test-writer   — pins acceptance checks down as behavior tests
bin/
  buildloop       — zero-dependency helper the skills call (next-id, log, open-questions, can-promote)
templates/
  AGENTS.md     — the operating manual to drop at your repo root and adapt
```

`bin/buildloop` is added to the Bash tool's `PATH` automatically while the plugin is enabled, so the skills call a bare `buildloop …` — no path interpolation, no `python -m`, no dependency on your project's layout. It only needs `python3` on the machine.

## Adopting it in a project

1. **Install the plugin** (see the repo root [README](../../README.md) for marketplace setup):
   ```
   /plugin marketplace add giovannibeltrame/buildloop
   /plugin install buildloop@buildloop
   ```
2. **Drop the operating manual in.** Copy `templates/AGENTS.md` to your repo root as `AGENTS.md` and work through the `[PROJECT: …]` notes — they mark the few places that need your stack's specifics (product docs, test commands, test-framework conventions, the non-product files list, and any extra gate agents). **Keep the section numbering intact** — the skills and agents reference rules by number (e.g. `AGENTS.md §6.2`).
3. **Create the docs tree** the skills expect:
   ```
   docs/buildloop/{epics,bugfixes,hypotheses,constants}/
   ```
4. **Add your project-specific gate agents** (optional). The plugin ships the generic `code-checker`, `doc-validator`, and `test-writer`. If your stack needs a `security-reviewer` or `ux-checker`, add them under `.claude/agents/` and list them in `AGENTS.md §3.5`.

## The flow at a glance

```
1 To be refined → 2 Refining → 3 Ready for backlog (gate)
  → 4 Planned → 5 In progress → 6 In review (gate) → 7 UAT (gate) → 8 Completed
```

Each transition has a mechanical owner; only prioritization (3 → 4) and the terminal flips are purely human. See `AGENTS.md` for the full table.
