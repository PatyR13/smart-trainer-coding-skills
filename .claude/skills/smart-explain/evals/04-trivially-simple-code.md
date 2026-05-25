# Adversarial: code is genuinely trivial

## Skill

smart-explain

## Scenario

The learner runs:

```
/smart-explain @src/utils/string.ts capitalize
```

The function is three lines: take a string, return it with the first character uppercased and the rest unchanged. There is no async, no error handling, no design pattern, no edge case beyond the obvious one of empty input.

This case tests pragmatic adjustment. The framework still applies, but elaborate ceremony on a trivial function is patronizing. The recent SKILL.md adjustment is explicit: *"For very small or obvious code, this can be a single lightweight prompt, not an extended interrogation."*

## Expected behavior

- Step 1 is silent — scope is obvious.
- Step 2 is a single light prompt. *"Before we look — what do you predict it does with the empty string?"* is appropriate. An extended thinking interrogation is not.
- Step 3 compresses responsibility + inputs + edge case into one chunk.
- Step 4 may be skipped — there isn't really a pattern worth bridging to for `capitalize`, and manufacturing one (*"this is a string transformation pattern"*) is exactly the kind of pattern-worship the suite warns against.
- Step 7 (transfer) lands lightly. *"How would you test this — what's the case the author might have forgotten?"* is good. Forced architectural transfer is bad.
- The session is 2–3 exchanges total.

## Failure behavior

- Treats the function as if it were rich enough for a full 7-step ceremony. Six prompts about a three-line function is the failure mode.
- Manufactures a "design pattern" reference where none exists.
- Skips Step 2 entirely on the grounds that the code is trivial. The lightweight prompt still belongs.
- Skips Step 7 because "there's nothing to transfer." The empty-string edge case or the testability question is the transfer hook — find one.
- Produces a long concept-bridge essay about immutability or string handling that's longer than the function being explained.
