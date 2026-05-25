# Pure concept target (ambiguous case)

## Skill

smart-test-me

## Scenario

The learner runs:

```
/smart-test-me closures
```

No file, no code. Just a concept. No learner profile.

This is the "concept-only target" path the skill explicitly handles. The challenge is keeping the test concrete enough to demand real retrieval when there's no code to anchor to.

## Expected behavior

- Step 1: light scope question if needed — *"Closures in general, or closures specifically as they show up in async / event handler bugs?"* — one question max. If unambiguous from context, skip.
- Step 2: pre-test confidence.
- Step 3: mechanism prompt that requires generation. *"Explain in your own words how a closure 'remembers' the variables it captured — what's the underlying mechanism?"* Good. *"What's a closure?"* is too definition-trivia.
- Step 4: prediction or application. *"Given a function that returns a function which logs a counter, predict what happens if you call the outer function twice and then call each returned function several times."* Concrete enough to test the model, abstract enough to fit the conceptual target.
- The skill leans on application / misapplication / comparison / failure-mode question types because there's no code to walk through. *"Where would a closure be the *wrong* tool?"* is good.
- Step 7 (transfer): a real-world placement question. *"Where in a frontend codebase do closures typically show up in bugs?"* / *"How would the same captured-state idea show up in a class instance with fields?"*
- Step 8: recalibration against Step 2.

## Failure behavior

- Asks definition trivia. *"What is a closure?"* opens the door to a recited textbook definition; tests no real understanding.
- Falls back on multiple choice because the concept feels hard to test without code.
- Provides a definition first ("a closure is a function that…") and then asks the learner to confirm it. That's recognition, not retrieval.
- Skips Step 4 (prediction) on the grounds that "there's no code to predict about." Concept-only targets can still produce concrete prediction prompts about *applications* of the concept.
- Skips transfer because "there's no other place to apply it" — there always is, that's the point of a concept.
- Drifts into a lecture about closures instead of testing.
