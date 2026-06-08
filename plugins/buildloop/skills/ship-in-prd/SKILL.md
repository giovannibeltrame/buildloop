---
name: ship-in-prd
description: Phase ④ — ship a build-gated feature: distill its fc + bp into a living feature-document, write the CHANGELOG, deploy, and release to a launch cohort behind a flag. Deploy ≠ release. Archives the working docs. Use when build-gate reports a bp green.
---

# /buildloop:ship-in-prd

Ship the feature. Distill the working docs into the living one, then deploy and release as separate acts ([AGENTS.md §4.9](AGENTS.md)). Apply the writing principles ([§4.7](AGENTS.md)).

## Precondition

`buildloop current-phase <bp>` reads `④ Ship it` (the build-gate advanced ③ → ④). If not, refuse and name the missing gate.

## Steps

1. **Questions loop:**
   - What changed, and which kind of change? → the CHANGELOG line.
   - Which behaviors/rules must keep working? → the e2e-guarded invariants.
   - Which strict technical facts must a maintainer know? → technical notes.
2. **Write the living feature-document** ([§3.5](AGENTS.md)). For a new feature, copy `templates/feature-document.md` to `docs/buildloop/living/<feature-name>.md`; for a re-ship (from `tweak-it`), update the existing one. Distill from the fc + bp:
   - **Hypotheses & metrics** — copied from the fc so `measure` can read them ([§4.10](AGENTS.md)).
   - **Invariants (e2e-guarded)** — each naming its `tests/e2e/` test.
   - **Release** — the flag name + launch cohort (step 4).
   - **Technical notes.**
   Write its log row (the living doc keeps only its **last** transition — on a re-ship, delete the prior transition row first):
   ```
   buildloop log docs/buildloop/living/<feature-name>.md ship-in-prd "shipped to launch cohort" --to "④ Ship it"
   ```
3. **Prepend a CHANGELOG.md entry** (repo root) — WHO changed WHAT, ≤280 chars, linked to the fc ([§5.1](AGENTS.md)).
4. **Deploy ≠ release** (§4.9):
   - **Deploy** — merge → main; CI/CD delivers the artifact, all-or-nothing. [PROJECT: name the pipeline.]
   - **Release** — set the feature flag to a small launch cohort and record the flag name + cohort in the feature-document. [PROJECT: name the flag mechanism.]
5. **Archive the working docs.** Move the fc and bp to `docs/buildloop/working/archive/` (their numbers stay reserved). The living feature-document is now the source of truth.
6. Hand off to `/buildloop:measure` once the cohort accrues live data.

## Notes

- Full rollout and rollback are flag flips, not redeploys (§4.9): `measure = yes` → flag to 100%; rollback → flag to 0%.
- Standalone: refuses if the bp is not build-gate green, naming the missing gate.
