# Clean symbol explanation (easy case)

## Skill

smart-explain

## Scenario

The learner runs:

```
/smart-explain @src/services/auth.ts loginUser
```

The file contains a single ~40-line async function that takes credentials, looks the user up, checks a password hash, and returns a session token (or null). The learner profile exists and records `experience_level: mid`, `primary_stack: ["TypeScript", "Node.js"]`, `explanation_depth: balanced`.

This is the canonical happy path: clean scope, clear symbol, reasonable profile, no friction.

## Expected behavior

- Step 1 is silent — scope is unambiguous; no clarifying question asked.
- Step 2 fires before any explanation. Some shape of *"Before I dig in — looking at the imports and signature, what would you expect this to do?"*
- After the learner's first-thinking answer, the skill chunks the explanation: responsibility / inputs+outputs first, then control flow, then decisions — not all in one wall.
- At least one between-chunk reasoning prompt appears (*"why do you think that guard exists?"*, *"what would happen if we removed this check?"*).
- A concept bridge appears — e.g. naming idempotency, dependency injection, or invariant-guarding depending on what the function reveals. The bridge is one sentence, not a paragraph.
- The session ends with a transfer question (Step 7) that requires the learner to apply the concept somewhere else — counterfactual, testing, or "where else in this codebase."
- Tone is mentor-like and concise. Acknowledgments are short.

## Failure behavior

- Opens with a complete top-to-bottom explanation of the function before asking what the learner thinks. (Solution dumping.)
- Skips Step 2 because the code "looks simple."
- Performs line-by-line narration (*"Line 1 imports… Line 2 imports…"*).
- Ends without a transfer question — the session feels like guided reading rather than learning.
- Bridges to multiple concepts in one turn for a mid-level learner (overload).
- Manufactures a pattern name where none cleanly applies. ("This is essentially a Visitor pattern" when it isn't.)
- Asks trivia (*"What's the return type?"*) or yes/no questions.
- Forces `/smart-init` despite the profile being present.
