---
name: build-plan
description: "Phase ② orchestrator: create a build plan (bp) from a feature candidate and fill it by sub-invoking ddd (WHAT), bdd (HOW), and the plan agent (decomposition), then run the plan-gate and advance ②→③. Full from a think-gated fc, or lite from tweak-it. Use when planning an approved candidate or a tweak."
---

# /buildloop:build-plan

Turn a candidate into an executable plan. Orchestrate the WHAT, HOW, and decomposition into one `bp-NNNN.md`, then gate it. Apply the writing principles (`${CLAUDE_PLUGIN_ROOT}/AGENTS.md` §1).

## Steps

1. **Mode.** *Full* (default) plans a fresh fc; it must be past the think-gate (its log shows phase `② Plan it`; check `buildloop current-phase <fc>`). *Lite* comes from `tweak-it`: thin and fast, WHY and narrative unchanged, still a real WHAT/HOW/plan but minimal.
2. **Create the bp.** `buildloop next-id bp`; copy `templates/bp-xxxx.md` to `docs/buildloop/working/bp-NNNN.md` (§2.5); set `Candidate:` to the fc and `Mode:`. Write the first row:
   ```
   buildloop log docs/buildloop/working/bp-NNNN.md build-plan "created from fc-NNNN" --to "② Plan it"
   ```
3. **Fill it** by sub-invoking, in order:
   - `/buildloop:ddd` → WHAT + Ubiquitous Language.
   - `/buildloop:bdd` → HOW (acceptance scenarios table).
   - the **`plan` agent** → phases, steps, tasks, critical files, trade-offs. Unfold every task to its minimum (the plan-gate's decomposition criterion).
4. **Check open questions** (`ddd` may have raised some):
   ```
   buildloop open-questions docs/buildloop/working/bp-NNNN.md
   ```
   If `open-questions`, stop and resolve them before gating (the anti-assumption discipline `ddd` owns).
5. **Gate and advance.** Invoke the `plan-gate` agent on the bp.
   - **Pass** → `buildloop log <bp> "plan-gate · build-plan advances" "gate passed" --to "③ Build it"`, then hand off to `/buildloop:tdd`.
   - **Fail** → address the gap list, re-run. Gates never write; the advancing skill does (§2.4).

## Notes

- Order matters: `bdd` builds on `ddd`'s WHAT, and the `plan` agent decomposes against both.
- Standalone: runs on a given fc, and flags an un-passed think-gate rather than refusing.
