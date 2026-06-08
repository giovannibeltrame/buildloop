---
name: ship-in-prd
description: Phase ④: ship a build-gated feature: distill its fc + bp into a living feature-document, write the CHANGELOG, deploy, and release to a launch cohort behind a flag. Owns the deploy ≠ release seam. Archives the working docs. Use when build-gate reports a bp green.
---

# /buildloop:ship-in-prd

Ship the feature. Distill the working docs into the living one, then deploy and release as separate acts. This skill owns the **deploy ≠ release** seam. Apply the writing principles (AGENTS.md §2).

## Deploy ≠ release

Keep the two apart; that separation is what makes rollout and rollback cheap.

- **Deploy** (the artifact reaches its runtime): all-or-nothing; the code is live or it isn't. [PROJECT: name your deploy pipeline.]
- **Release** (who sees it): a feature flag or cohort gate in the app, not infra. Set the flag to a small launch cohort and record the flag name and cohort in the feature-document. [PROJECT: name your flag mechanism.]
- Full rollout (`measure` = yes) flips the flag to **100%**. Rollback flips it to **0%**, no redeploy.

## Precondition

`buildloop current-phase <bp>` reads `④ Ship it` (the build-gate advanced ③ → ④). If not, refuse and name the missing gate.

## Steps

1. **Questions loop:**
   - What changed, and which kind of change? → the CHANGELOG line.
   - Which behaviors or rules must keep working? → the e2e-guarded invariants.
   - Which strict technical facts must a maintainer know? → technical notes.
2. **Write the living feature-document** (§1.5). For a new feature, copy `templates/feature-document.md` to `docs/buildloop/living/<feature-name>.md`; for a re-ship (from `tweak-it`), update the existing one. Distill from the fc and bp:
   - **Hypotheses & metrics**: copied from the fc so `measure` can read them (the telemetry seam, §1.8).
   - **Invariants (e2e-guarded)**: each naming the e2e test that guards it.
   - **Release**: the flag name and launch cohort.
   - **Technical notes.**
   Write its log row (the living doc keeps only its **last** transition, so on a re-ship delete the prior transition row first):
   ```
   buildloop log docs/buildloop/living/<feature-name>.md ship-in-prd "shipped to launch cohort" --to "④ Ship it"
   ```
3. **Prepend a CHANGELOG.md entry** (repo root): WHO changed WHAT, ≤280 chars, linked to the fc.
4. **Deploy, then release** per the seam above: ship the artifact through the deploy pipeline, then set the flag to the launch cohort.
5. **Archive the working docs.** Move the fc and bp to `docs/buildloop/working/archive/` (their numbers stay reserved). The living feature-document is now the source of truth.
6. Hand off to `/buildloop:measure` once the cohort accrues live data.

## Notes

- Standalone: refuses if the bp is not build-gate green, naming the missing gate.
