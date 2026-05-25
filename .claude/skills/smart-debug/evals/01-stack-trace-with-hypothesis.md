# Stack trace with hypothesis (easy case)

## Skill

smart-debug

## Scenario

The learner runs:

```
/smart-debug @src/api/client.ts "Cannot read properties of undefined (reading 'data')"
```

…and follows up with:

> "I think the response is sometimes empty when the network call times out — but I'm not sure."

A stack trace is implied by the error string and there's a clear, plausible hypothesis from the learner. The learner profile exists and records `experience_level: mid`.

This case validates the canonical hypothesis-driven flow.

## Expected behavior

- Step 1: the symptom is already clear (`Cannot read properties of undefined`). The skill acknowledges and does not re-interrogate.
- Step 2: the learner already provided a hypothesis. The skill captures it and moves forward.
- Step 3: the skill explicitly distinguishes facts from assumptions. *"The error text and that it happens for some calls — those are facts. That the response is empty after a timeout — that's an assumption right now, since we haven't verified it. Want to add a log to confirm?"*
- Step 5: the skill asks the falsification question for the hypothesis. *"If your hypothesis is right, what would we see in the response object when the bug fires? What would we see if it's wrong?"*
- Step 6: the skill investigates the actual code path before concluding. Reading the file, looking at how the response is handled.
- Step 7: once root cause is found, the learner must articulate it (*"so what was the chain that produced the symptom?"*).
- Step 8: at least one prevention answer with specificity. *"A guard at the response layer"* is fine. *"Add a test"* alone is not — push for *what property the test would assert.*

## Failure behavior

- Opens with a code patch (*"here, change line 14 to…"*). No hypothesis testing.
- Accepts the learner's hypothesis as fact without falsification probe.
- Produces a list of speculative possible causes (*"could be timeout, could be auth, could be CORS, could be…"*). Shotgun debugging.
- Skips Step 3 — never explicitly distinguishes facts from assumptions. (This is the keystone habit the skill teaches.)
- Skips Step 8 — fixes the bug, session ends. No prevention thinking.
- Asserts a confident root cause based on guessing at parts of the code it hasn't actually read.
- Suggests "just restart the server" or other surface-level non-explanations.
