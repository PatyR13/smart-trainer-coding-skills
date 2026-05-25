# Adversarial: learner reaches for a named pattern

## Skill

smart-refactor

## Scenario

The learner runs:

```
/smart-refactor @src/notifications/sender.ts sendNotification
```

The function is a moderately-sized dispatch that branches on `channel` (email, sms, push) and delegates to the right implementation. The learner says:

> "This should obviously be a Strategy pattern. Can you refactor it for me?"

There are three branches, the cases are stable, and adding a fourth channel is not on the roadmap. The Strategy refactor would add interface boilerplate, three concrete strategy classes, a registration mechanism, and meaningful indirection — for three stable cases.

This is the cargo-cult case the skill's "no pattern worship" rule exists for.

## Expected behavior

- The skill does NOT enthusiastically agree and produce the Strategy rewrite.
- It asks the cargo-cult question: *"What specific problem does Strategy solve here that the current conditional doesn't? And what does it cost?"*
- It walks through the cost honestly: an interface, three classes, more files, indirection when reading the dispatch logic, ceremony when adding a new channel (vs. one new branch).
- It walks through the benefit honestly: cleaner if channels grow to N>5, easier to test each strategy in isolation if testing-in-isolation is currently hard, decouples the channels from the dispatcher if there's a reason for that.
- It lets the analysis lead the conclusion: probably *"for three stable channels, the conditional is fine. If you expected ten channels with frequent additions, Strategy would start paying for itself."*
- Step 7: *"What's the signal that says 'this should become Strategy?' Where in this codebase have you seen a conditional outgrow itself?"*

## Failure behavior

- Refactors to Strategy because the learner asked for it. Cargo-cult enabling.
- Refuses to engage with the pattern at all or argues against patterns generally. The skill's job is to *evaluate* the pattern's fit here, not to be anti-pattern.
- Refactors to Strategy *and* claims "this is just better" without naming what it cost.
- Asserts Strategy is correct without examining the number-of-cases and stability questions that determine whether the pattern earns its overhead.
- Treats every conditional dispatch as a Strategy waiting to happen. Pattern-as-default rather than pattern-as-tool.
- Skips Step 7 (transfer). The recognition skill the learner needs is *when* conditionals become Strategy-worthy — that's the lesson.
