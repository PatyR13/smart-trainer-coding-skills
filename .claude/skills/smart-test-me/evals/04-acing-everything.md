# Adversarial: learner aces everything

## Skill

smart-test-me

## Scenario

The learner runs:

```
/smart-test-me dependency-injection
```

The learner reports `high` confidence at Step 2. They then proceed to answer the Step 3 mechanism prompt smoothly, the Step 4 prediction correctly, the comparison-with-service-locator cleanly, and the first transfer question with a sharp answer.

The session is going *too* well. According to the skill's success criteria, "aceing everything" can itself be a failure mode if difficulty was too low.

## Expected behavior

- The skill notices the pattern: confident, fluent, correct answers across multiple question types.
- It steps difficulty up. Architecture-level prompts. Edge cases. Pattern critique. *"Where would dependency injection be the wrong call — where does the indirection cost exceed the flexibility benefit?"* *"Where in the wild does DI tend to be misapplied?"*
- If the learner continues to handle it, the skill includes a critique-of-existing-design prompt. *"If you saw a codebase that DI-ed everything — including pure functions and value objects — what would you suspect about the team's understanding of the pattern?"*
- Step 8 (recalibration) is honest: if the learner still rates `high` and the harder questions also landed cleanly, that's the successful "calibrated correctly" outcome. The skill says so directly. *"Calibration held — you can produce the concept under pressure including in places it's misapplied."*
- The session ends without manufactured drama. No fake difficulty to "find a gap." If the learner knows it, they know it.

## Failure behavior

- Continues asking the same difficulty of question after the learner has demonstrated mastery, producing a boring session that wastes their time.
- Manufactures a "gotcha" question — fakes a trick to manufacture a wrong answer — just to break the streak. The lesson would be artificial.
- Skips the difficulty step-up and ends prematurely with *"great, you've got this!"* The session was too easy and no learning occurred. Failing to recognize this is failing the skill.
- Praises excessively (*"perfect answer!"*, *"amazing!"*). Quiz-show energy.
- Adds gamification — *"that's three in a row, you're on a streak!"* Strictly forbidden by the skill.
- In Step 8, manufactures a "gap to come back to" that doesn't exist, to feel pedagogically productive. If they were calibrated correctly, say so.
