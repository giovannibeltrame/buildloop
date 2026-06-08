---
name: log
description: Append a row to a buildloop doc's ## Log table (the single source of truth for which phase the doc is in) and surface the matching commit footer. The helper every other buildloop skill and gate-advance calls at transition time. Use when a phase advances, on intra-phase work worth recording, or when the user signals a human-owned step ("I prioritized…", "I approved the UAT").
---

# /buildloop:log

The **single writer** of the `## Log` table. That table is the only source of truth for "where are we"; there is no `Status:` field. Apply the log-table state model (AGENTS.md §2.2); do not restate it.

Two row kinds, fixed four columns `when | who | phase → | what`:

- **work row**: intra-phase progress; `phase →` is left as `—`.
- **transition row**: a phase advance; `phase →` carries `<From> → <To>`. The current phase is the To-side of the most recent transition row.

## Steps

1. Identify the doc, the `who`, and the `what` (≤280 chars):
   - `who` folds the auto/human distinction. A named skill/agent is automated; the literal `human` is a manual step. For a gate advance, name both: `<gate> · <advancer> advances`.
2. Append the row (append-only, newest at bottom; never edit an existing row):
   - work row:
     ```
     buildloop log <doc-path> "<who>" "<what>"
     ```
   - transition row (only `--to`; From is auto-filled from the doc's current phase):
     ```
     buildloop log <doc-path> "<who>" "<what>" --to "<② Plan it>"
     ```
   Phase tokens are canonical: `① (Re)Think it · ② Plan it · ③ Build it · ④ Ship it`.
3. On a transition, surface buildloop's transition-commit footer for whoever commits it. This footer convention is owned here; it mirrors the log row into git history:
   ```
   Log: <doc-id> <From> → <To>
   ```

## Notes

- **Gates never write.** A gate returns a verdict; on pass the advancing skill (or, for a standalone gate run, the user/Claude) calls this skill to append the transition row.
- **Living-doc retention.** Working docs (`fc`/`bp`) keep the full log until they archive on ship. A `feature-document.md` keeps only its last transition; change history lives in `CHANGELOG.md`. That trimming happens when `ship-in-prd` distills the doc, not here.
- For human-owned steps, suggest logging in the moment rather than waiting.
