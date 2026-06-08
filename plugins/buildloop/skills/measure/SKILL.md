---
name: measure
description: Phase ④: judge a shipped feature's hypotheses against live prod data from the launched cohort, and record a rollout verdict (yes / not yet / invalidated). Closes the hypothesis loop. Use when a shipped feature's cohort has accrued enough telemetry to judge.
---

# /buildloop:measure

Judge the bet. Compare the launched cohort's live metrics to the thresholds set in `interview-me`, and decide rollout. This closes the hypothesis loop (AGENTS.md §1.8). Apply the writing principles (§2).

## Precondition

A `feature-document.md` with **Hypotheses & metrics** defined and a released cohort. If no metrics are defined, refuse; there is nothing to judge.

## Steps

1. Read the feature-document's **Hypotheses & metrics** (thresholds) and **Release** (cohort).
2. Read the cohort's **live prod data** from the telemetry sink and compare each metric to its threshold. [PROJECT: name the dashboard / query / metrics store, per §1.8.]
3. Record the **verdict** in the feature-document's metrics table, one of:
   - **yes**: threshold met. Flip the flag to **100%** (full rollout); set Release state = 100%. The feature is **Shipped**. Log a work row.
   - **not yet**: inconclusive. Hand off to `/buildloop:tweak-it` to improve, keeping the cohort as-is.
   - **invalidated**: threshold clearly missed, so the premise was wrong. Re-enter the loop: `buildloop log <feature-doc> "measure · re-think" "invalidated" --to "① (Re)Think it"`, then hand off to `/buildloop:interview-me` to re-sharpen or drop.
4. Update the verdict column and log the outcome.

## Notes

- The chain that feeds this: metric and threshold defined in ① (fc), instrumented in ③ (tdd), released to a cohort in ④ (ship-in-prd), judged here in ④ (§1.8).
- Rollout and rollback are flag flips, not redeploys (the deploy ≠ release seam `ship-in-prd` owns).
- Standalone: refuses if the feature-document defines no metrics.
