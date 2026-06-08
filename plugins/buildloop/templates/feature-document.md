<!--
Feature document — TEMPLATE (living doc, phase ④ Ship it onward).
`ship-in-prd` creates it by distilling the fc + bp (which then archive), and
`measure` reads Hypotheses & metrics against live prod data. `tweak-it` makes
minimal per-change updates. Lives in docs/buildloop/living/, one per feature.
Unlike the working docs it keeps only the LAST transition in its log; full
change history lives in CHANGELOG.md.
-->

# <feature name>

## Summary

<what the feature is and does, from the user/system perspective>

## Hypotheses & metrics

<distilled from the fc; `measure` compares the cohort's values to the threshold>

| hypothesis | metric | threshold | telemetry source | verdict |
|---|---|---|---|---|
| <H1> | <metric> | <threshold> | <dashboard/query/store> | <pending \| yes \| not yet \| invalidated> |

## Invariants (e2e-guarded)

<behaviors the system must keep upholding; each names the e2e test that guards it>

- <invariant> — `tests/e2e/<…>`

## Release

- Flag: `<flag-name>`
- Cohort: <launch cohort>
- State: <launch cohort \| 100% \| 0%>

## Technical notes

<strict technical facts a maintainer must know: deploy target, integrations, gotchas>

## Log

<last transition only — change history lives in CHANGELOG.md>
<!-- ship-in-prd writes the row: buildloop log <doc> ship-in-prd "shipped to launch cohort" --to "④ Ship it" -->

| when | who | phase → | what |
|---|---|---|---|
