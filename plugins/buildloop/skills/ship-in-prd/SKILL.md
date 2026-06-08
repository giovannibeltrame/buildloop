---
name: ship-in-prd
description: Phase ④ — ship a build-gated feature: distill its fc + bp into a living feature-document, write the CHANGELOG, deploy, and release to a launch cohort behind a flag. Owns the deploy ≠ release seam. Archives the working docs. Use when build-gate reports a bp green.
---

# /buildloop:ship-in-prd

Ship the feature. Distill the working docs into the living one, then deploy and release as separate acts. This skill owns the **deploy ≠ release** seam. Apply the writing principles (AGENTS.md §2).

## Deploy ≠ release

The two are separate acts, and keeping them separate is what makes rollout and rollback cheap:

- **Deploy** (artifact reaches the box): CI/CD, all-or-nothing. [PROJECT: name the pipeline, e.g. GitHub Actions on merge → main: build, deliver to the host, restart via systemd / docker compose. The code is on the box or it isn't.]
- **Release** (who sees it): a feature flag / cohort gate in the app, not infra. Set the flag to a small launch cohort and record the flag name + cohort in the feature-document. [PROJECT: name the flag mechanism. A single host can't split traffic without an LB + target groups — YAGNI until a flag stops being enough.]
- Full rollout (`measure` = yes) = flip the flag to **100%**. Rollback = flip to **0%**, no redeploy.

## Precondition

`buildloop current-phase <bp>` reads `④ Ship it` (the build-gate advanced ③ → ④). If not, refuse and name the missing gate.

## Steps

1. **Questions loop:**
   - What changed, and which kind of change? → the CHANGELOG line.
   - Which behaviors/rules must keep working? → the e2e-guarded invariants.
   - Which strict technical facts must a maintainer know? → technical notes.
2. **Write the living feature-document** (§1.5). For a new feature, copy `templates/feature-document.md` to `docs/buildloop/living/<feature-name>.md`; for a re-ship (from `tweak-it`), update the existing one. Distill from the fc + bp:
   - **Hypotheses & metrics** — copied from the fc so `measure` can read them (the telemetry seam, §1.8).
   - **Invariants (e2e-guarded)** — each naming its `tests/e2e/` test.
   - **Release** — the flag name + launch cohort.
   - **Technical notes.**
   Write its log row (the living doc keeps only its **last** transition — on a re-ship, delete the prior transition row first):
   ```
   buildloop log docs/buildloop/living/<feature-name>.md ship-in-prd "shipped to launch cohort" --to "④ Ship it"
   ```
3. **Prepend a CHANGELOG.md entry** (repo root) — WHO changed WHAT, ≤280 chars, linked to the fc (§3.1).
4. **Deploy, then release** per the seam above: merge → main triggers CI/CD; set the flag to the launch cohort.
5. **Archive the working docs.** Move the fc and bp to `docs/buildloop/working/archive/` (their numbers stay reserved). The living feature-document is now the source of truth.
6. Hand off to `/buildloop:measure` once the cohort accrues live data.

## Notes

- Standalone: refuses if the bp is not build-gate green, naming the missing gate.
