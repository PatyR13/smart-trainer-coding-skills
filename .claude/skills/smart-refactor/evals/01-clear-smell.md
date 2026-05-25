# Clear smell, possible intent (easy case)

## Skill

smart-refactor

## Scenario

The learner runs:

```
/smart-refactor @src/services/order.ts placeOrder
```

The function is ~120 lines, mixes input validation, business logic, two database calls, an external API call, and an audit log entry. By textbook standards it has several smells (long function, mixed responsibilities, defensive complexity at the top). But it's also the system's main orchestrator function and may be deliberately flat for debuggability.

This is the canonical happy path: clear smell candidates, but with a real plausible intent reading.

## Expected behavior

- Step 1: the skill asks about intent before naming smells. *"Before we look for problems — what do you think this function is optimized for? It looks like an orchestrator — is it deliberately flat for debuggability?"*
- Step 2: 3–4 candidate smells ranked, not a dump of every possible thing. *"The most pressing thing I'd point at is the mixed validation/business/IO. Less critically there's the defensive checks at the top and the length itself."*
- Step 3 (tradeoff analysis): for each candidate refactor, explicit cost-and-benefit articulation. *"Extracting the validation block improves local clarity of the orchestrator; it costs one more file to open and removes context that some maintainers actually want."*
- Step 4: at least two alternatives compared — e.g. extract-helpers vs. push-validation-up-to-the-caller vs. leave-it-alone-and-add-comments.
- Step 5: escalation ladder is climbed gradually. If a refactor is agreed to, the skill nudges the learner to propose the move first.
- Step 6 (compare versions): honest accounting of what improved and what got more complex. If only "looks cleaner" is the improvement, the skill flags this as elegance-theater risk.
- Step 7 (transfer): *"Where else in this codebase do you suspect the orchestrator pattern is doing too much?"* / *"What's the signature of orchestrators that *are* working well — what's different about them?"*

## Failure behavior

- Opens with a refactored version of the function. (Solution dumping.)
- Skips Step 1 (intent). Treats the function as a problem to fix without reading why it might exist this way.
- Enumerates every possible smell without ranking. Overwhelms the learner.
- Articulates only benefits ("this is cleaner!"), no costs. Tradeoff analysis missing.
- Pushes a named pattern (Command, Strategy) without justifying what problem it solves here.
- Refactors aggressively without ever asking whether the change is worth its cost.
- Skips Step 6 (compare). The learner never sees the cost they accepted.
- Skips Step 7 (transfer). One function gets cleaner, the learner is no smarter.
