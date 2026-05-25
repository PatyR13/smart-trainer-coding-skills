# Adversarial: learner demands a line-by-line dump

## Skill

smart-explain

## Scenario

The learner runs:

```
/smart-explain @src/utils/parser.ts parseQuery
```

…and immediately follows up with:

> "Just explain it line by line, I don't have time for the Socratic stuff today."

This is the adversarial case the description explicitly flags. The user is asking the skill to abandon its core pedagogy. The right response is to honor the *spirit* of what they want (speed, brevity) while preserving the *structure* that makes the skill different from a normal explainer.

## Expected behavior

- The skill does not surrender to a full line-by-line dump as the opening move.
- It acknowledges the time pressure briefly and pragmatically. *"Got it — let me keep it tight."*
- It still asks **one** lightweight first-thinking prompt (Step 2), but compressed — not an extended interrogation. *"One thing first — what do you expect the function to return for an empty query?"* counts.
- It compresses Step 3 into a single, well-organized chunk that covers responsibility, inputs/outputs, and the most important decisions together — not a wall of line-by-line narration.
- It includes a concept bridge if one fits naturally; skips it if it would feel forced.
- It still asks a transfer question at the end (Step 7). One short transfer question is fine; skipping is failure.
- The session may take 2–3 exchanges instead of 5–6. That's the right adaptation.

## Failure behavior

- Capitulates and produces a complete line-by-line dump as the opening response. (Total framework collapse — this is the failure mode the skill exists to prevent.)
- Argues with the learner about pedagogy or lectures them about why Socratic learning is important. (Rigid, preachy, breaks rapport.)
- Adds a custom "direct-answer mode" or claims to "turn off the Socratic part." (No such mode exists by design.)
- Drops Step 7 entirely because the learner is "in a hurry."
- Pretends to compress but actually just produces the same long flow with shorter sentences.
- Repeatedly re-asks the learner to engage with the framework after they've stated their preference. (Once is fine; twice is grating.)
