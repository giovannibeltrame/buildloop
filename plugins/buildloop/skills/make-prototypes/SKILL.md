---
name: make-prototypes
description: Phase ① — produce lo-fi UX prototypes for a feature candidate (fc), one per option worth weighing, so does-it-worth has something concrete to judge. Use after interview-me on an fc, or when asked to sketch options for a candidate.
---

# /buildloop:make-prototypes

Make the candidate tangible. Produce lo-fi prototypes in the fc's `## Prototypes` section so `does-it-worth` can judge them. Apply the writing principles ([AGENTS.md §4.4](AGENTS.md)); keep it cheap and disposable.

## Steps

1. Read the fc's Why / Narrative / Hypotheses ([§3.5](AGENTS.md)). If no fc exists, stub a minimal one or point the user at `/buildloop:interview-me`.
2. Sketch **lo-fi** prototypes — ASCII wireframes, flow sketches, or short interaction outlines. One per genuinely distinct option; do not gold-plate (YAGNI). Each should convey the narrative, not the visual polish.
3. Write them into the fc's `## Prototypes` section, labelled so a verdict can reference them.
4. Record progress with a work row:
   ```
   buildloop log docs/buildloop/working/fc-NNNN.md make-prototypes "N prototype(s)"
   ```
5. Hand off to `/buildloop:does-it-worth`.

## Notes

- Lo-fi by design — prototypes are throwaway thinking aids, not implementation. Real UX validation is the `ux-checker` agent at the build-gate.
- Standalone: requires an fc; asks for or stubs one if missing.
