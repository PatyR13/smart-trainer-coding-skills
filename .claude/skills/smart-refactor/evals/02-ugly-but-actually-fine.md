# Ugly-looking code that's actually fine

## Skill

smart-refactor

## Scenario

The learner runs:

```
/smart-refactor @src/parser/tokenize.ts tokenize
```

The function is a ~80-line manual character-by-character loop with several nested switch-like conditionals. To a refactoring-eager learner it screams *"long function, large conditionals, primitive obsession."*

But: the function is a hot-path tokenizer. The flat structure exists for performance (fewer function calls in a tight loop) and debuggability (a single stack frame). The "primitive obsession" is deliberate — wrapping each character in a typed value would allocate and tank throughput.

This is the case the skill's "when this is actually fine" discipline exists for. Refactoring would make the code worse.

## Expected behavior

- Step 1: the skill asks about intent and recognizes the performance/hot-path framing — either inferring from context (file location, function name, code shape) or eliciting from the learner.
- The skill flags the candidate smells but immediately offers the intent reading alongside. *"Length-wise it would be a long function in most contexts. Conditionals-wise, classically I'd point at the nested switches. But this is a tokenizer hot path — the flat shape and primitive operations are almost certainly load-bearing for performance."*
- Step 3 (tradeoff analysis) explicitly addresses cost: a refactor here would trade clarity (small win) for performance (real loss in a hot loop).
- The skill lands on *"don't refactor"* as a conclusion. This is an explicitly legitimate success outcome per the SKILL.md.
- Step 7 still runs — *"how would you recognize hot-path code in this codebase that should be left alone? What's the signal that says 'don't apply the standard cleanups here'?"* — turning the case itself into a teaching moment.

## Failure behavior

- Identifies the smells and proposes a clean refactor without recognizing the performance constraint. Cargo-cult refactoring in its purest form.
- Acknowledges the performance consideration but refactors anyway because "the code is hard to read." Elegance theater overriding intent.
- Wraps each character in a typed value (newtype) without acknowledging the allocation cost. Pattern application without cost analysis.
- Splits the function into helpers and claims the result is "just as fast" without evidence.
- Treats *"don't refactor"* as a failure of the session rather than as the correct outcome. Manufactures a refactor to avoid an empty diff.
- Skips Step 7 because "we didn't actually do anything." The recognition lesson — when to leave hot paths alone — is the most valuable teaching moment in the session.
