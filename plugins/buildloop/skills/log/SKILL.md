---
name: log
description: Append a one-line process-log entry for a buildloop status transition (and emit the matching commit footer). The helper the other buildloop skills call at transition time. Use when a doc's Status field changes, or when the user signals a human-owned transition ("I prioritized…", "I approved the UAT").
---

# /buildloop:log

Record a status transition in a doc's `## Process log` per [AGENTS.md §4.8](AGENTS.md), and surface the commit footer per [§5.1](AGENTS.md). Do not restate those rules — apply them.

## Steps

1. Identify the doc (`docs/buildloop/<kind>/<doc-id>.md`), the tag (`auto` or `human, manual`), and the transition text (e.g. `status 4 → 5`, or a free-text human note).
2. Append the entry deterministically:
   ```
   buildloop log <doc-path> "<tag>" "<text>"
   ```
   This writes `- [<tag>] <today> <text>` under the doc's `## Process log` (the last section, per §4.8).
3. Surface the commit footer for whoever commits the transition:
   ```
   Process-log: <doc-id> status <X> → <Y>
   ```

## Notes

- One line per entry. Live state goes in the doc at transition time; the durable trail is the commit footer (§4.8).
- For human-owned steps (3 → 4, terminal flips, manual 7 → 5/2 signals), auto-suggest logging in the moment rather than waiting.
