# Ambiguous file scope

## Skill

smart-explain

## Scenario

The learner runs:

```
/smart-explain @src/api/client.ts
```

No symbol is specified. The file contains seven top-level functions of roughly similar size — a constructor-style factory, two private helpers, three exported request methods, and an error-mapper. The file has no obvious focal element. No learner profile exists.

This case tests Step 1 (scope clarification) and the rule against over-clarifying.

## Expected behavior

- The skill skims the file and recognizes that there isn't one obvious focal element.
- It asks **one** short clarifying question proposing a reasonable default and offering an alternative. Examples that pass: *"Whole module, or one of the request methods? If one — which feels least clear?"* / *"Looks like the most central thing is the factory, but if a specific method is what's bugging you, let's start there."*
- After the learner answers, the skill proceeds to Step 2 with the chosen scope.
- The skill proceeds with balanced defaults rather than forcing `/smart-init`. At most one closing-line suggestion about `/smart-init` is acceptable; insisting is failure.

## Failure behavior

- Silently picks one function and starts explaining it without confirming. (The learner came in with something in mind.)
- Enumerates all seven functions and asks the learner to choose from the full list. (Over-clarifying — moves the cognitive load onto the learner.)
- Asks multiple clarifying questions before any progress. (Step 1 should be one question.)
- Refuses to proceed without the profile or makes onboarding feel like a gate.
- Picks scope arbitrarily then can't recover when the learner says *"wait, I meant the error mapper."*
