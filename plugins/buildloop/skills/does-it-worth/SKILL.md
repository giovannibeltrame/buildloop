---
name: does-it-worth
description: Phase ① filter — judge a feature candidate's prototypes and record a verdict (yes / not yet / park / never). On "yes" it runs the think-gate and, on pass, advances the fc to ② Plan it. Use after make-prototypes on an fc, or when deciding whether a candidate is worth building.
---

# /buildloop:does-it-worth

Filter the candidate before it costs anything to plan. Apply the writing principles ([AGENTS.md §4.4](AGENTS.md)); never guess (the anti-assumption discipline `ddd` owns).

## Steps

1. Read the fc with its prototypes ([§3.5](AGENTS.md)). If prototypes are missing, refuse and name what's needed.
2. **Questions loop:** Is it worth building (per prototype)? Which prototype best conveys the narrative? Resolve open questions; if any remain, capture them and stop — a verdict on a guess is forbidden.
3. Write the **verdict** into the fc's `## Verdict` section, one of:
   - **yes** — proceed to the gate (step 4).
   - **not yet** — reloop to `/buildloop:interview-me` to sharpen WHY / narrative / hypotheses.
   - **park** — shelve; revisit via `interview-me` later.
   - **never** — drop, keep the learning.
   Record the verdict as a work row: `buildloop log <fc> does-it-worth "verdict: <…>"`.
4. **On "yes", run the gate.** Invoke the `think-gate` agent on the fc.
   - **Pass** → advance: `buildloop log <fc> "think-gate · does-it-worth advances" "gate passed" --to "② Plan it"`, then hand off to `/buildloop:build-plan`.
   - **Fail** → address the gap list it returns, then re-run. Do not advance (§3.4 — gates never write; the advancing skill does).

## Notes

- Only "yes" reaches the gate; the other three verdicts end or reloop phase ① without advancing.
- Standalone: refuses if prototypes are absent, naming the gap.
