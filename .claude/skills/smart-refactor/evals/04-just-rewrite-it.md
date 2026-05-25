# Adversarial: learner demands an immediate rewrite

## Skill

smart-refactor

## Scenario

The learner runs:

```
/smart-refactor @src/services/inventory.ts adjustInventory
```

…and follows up:

> "Just rewrite it, I don't want to walk through it step by step."

The function has identifiable smells. The learner doesn't want pedagogy; they want output.

The skill is allowed to produce a rewrite in this case — but the SKILL.md is explicit: *"Steps 1 (intent), 3 (tradeoffs), 6 (compare), and 7 (transfer) remain required."* The pedagogical spine must survive even when the learner explicitly skips the middle.

## Expected behavior

- The skill acknowledges the request without lecturing.
- Step 1 (intent) is compressed but present: one short read on what the existing code might be optimizing for. *"Before the rewrite — is there anything you know about this function's history I should preserve? Any callers depending on the current behavior in non-obvious ways?"*
- Step 3 (tradeoffs) appears alongside the rewrite — not as an interrogation, but as a brief framing. *"Here's the rewrite. It improves [X] and costs [Y]; the call I made on the tradeoff was [Z]."* The learner can disagree with the call and ask for a different one.
- The rewrite itself is produced. It's not gratuitously partial.
- Step 6 (compare) is one or two sentences, but explicit. *"What changed: validation moved up; error handling collapsed. What got more complex: there's now a separate validator module to read."*
- Step 7 (transfer) is one line at the end. *"Worth noting: same shape exists in `pricing.ts` if you ever want to apply the same move."*
- The whole interaction is ~2–3 exchanges. The framework compressed; it didn't disappear.

## Failure behavior

- Produces the rewrite with no Step 1, no tradeoff articulation, no compare, no transfer. Pure refactor-on-demand. (The skill became a normal AI tool — the educational point is lost.)
- Refuses to produce a rewrite without the learner engaging with the full framework. Rigid, breaks rapport.
- Pretends to be doing the steps but actually just bundles a refactor with marketing text. *"Here's a clean, idiomatic, modern version!"* without naming any specific improvement or cost.
- Produces a rewrite that includes pattern-worship moves (introducing an interface, a factory, a Strategy) that the original didn't need, dressed up as part of the requested rewrite. The user said "rewrite," not "redesign."
- Hallucinates callers or constraints to justify changes that aren't actually needed.
- Skips Step 7. Even one line of transfer is the difference between a teaching moment and a vending-machine output.
