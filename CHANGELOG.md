# Changelog

All notable changes to this project, newest first.

## [0.1.0] — 2026-06-02

Initial release of the `buildloop` plugin and its marketplace.

- Four-phase loop — (Re)Think it → Plan it → Build it → Ship it — operationalized as twelve skills.
- Four review agents gating the phase transitions: `think-gate`, `plan-gate`, `code-checker`, `ux-checker`.
- A bundled, project-agnostic `AGENTS.md` methodology that the skills and agents read via `${CLAUDE_PLUGIN_ROOT}`.
- A zero-dependency `bin/buildloop` helper (`next-id`, `doc-id`, `current-phase`, `open-questions`, `log`); doc phase is derived from each doc's `## Log` table, never a stored status.
- Working- and living-doc templates (`fc-NNNN`, `bp-NNNN`, `feature-document`, `CHANGELOG`).

[0.1.0]: https://github.com/giovannibeltrame/buildloop/releases/tag/buildloop--v0.1.0
