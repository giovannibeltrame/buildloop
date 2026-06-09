# Changelog

All notable changes to this project, newest first.

## [0.1.1] — 2026-06-09

Documentation and packaging only. No change to skill or agent behavior.

- Split the READMEs: the root covers the marketplace, the plugin README stands alone as the adoption guide.
- Added a References section citing DDD, BDD, TDD, DRY, and the Spotify product-building workflow.
- Documented running any skill or agent standalone, and spelled out the three `docs/buildloop/` folders.
- Root `CLAUDE.md` is now a symlink to `AGENTS.md`.

## [0.1.0] — 2026-06-02

Initial release of the `buildloop` plugin and its marketplace.

- Twelve skills driving a four-phase loop: (Re)Think it → Plan it → Build it → Ship it.
- Four review agents gating the phase transitions: `think-gate`, `plan-gate`, `code-checker`, `ux-checker`.
- A bundled, project-agnostic `AGENTS.md` methodology that the skills and agents read via `${CLAUDE_PLUGIN_ROOT}`.
- A zero-dependency `bin/buildloop` helper (`next-id`, `doc-id`, `current-phase`, `open-questions`, `log`); doc phase is derived from each doc's `## Log` table, never a stored status.
- Working- and living-doc templates (`fc-NNNN`, `bp-NNNN`, `feature-document`, `CHANGELOG`).

[0.1.1]: https://github.com/giovannibeltrame/buildloop/releases/tag/buildloop--v0.1.1
[0.1.0]: https://github.com/giovannibeltrame/buildloop/releases/tag/buildloop--v0.1.0
