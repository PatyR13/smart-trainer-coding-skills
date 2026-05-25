# Concrete code target (easy case)

## Skill

smart-test-me

## Scenario

The learner runs:

```
/smart-test-me @src/auth/login.ts loginUser
```

The function is a moderately interesting login flow — credential check, hash comparison, session creation, audit log. The learner profile exists; mid-level Node.js engineer with `explanation_depth: balanced`. The learner has read the file before.

This is the canonical happy path for the skill.

## Expected behavior

- Step 1 is silent (target is unambiguous).
- Step 2 (pre-test confidence) fires before any question. The learner answers *"medium"* and the skill anchors that for later.
- Step 3 (active recall) is an open generation prompt — *"Walk me through what `loginUser` does, from memory, as if to a colleague who hasn't seen the file."* Open generation, not a yes/no or trivia.
- Step 4 (prediction) is concrete and specific — *"what happens if the password hash comparison succeeds but the user record has been soft-deleted?"*
- Step 5 (diagnosis) correctly distinguishes misconception / incomplete / uncertain when the learner's answer falls short. Confident wrong reasoning is met with a falsifying question. Hedged not-knowing is met with a fact, not Socratic theater.
- Step 7 (transfer) fires — *"how would you test this?"* / *"where else in the codebase do you suspect a similar invariant?"* / *"how would the design change if 2FA were added?"*
- Step 8 (recalibration) compares against the Step 2 anchor explicitly. *"You said 'medium' at the start — where are you now?"* If their confidence dropped, the skill frames it as a successful outcome.

## Failure behavior

- Skips Step 2. (No anchor → Step 8 becomes hollow.)
- Asks any multiple choice or true/false question. *"Did `loginUser` (a) hash, (b) compare, (c) both, or (d) neither?"* — instant failure.
- Asks trivia (*"what's the return type?"*) instead of generation.
- Confirms the learner's answer immediately after Step 3 instead of using it to plan Step 4 or 5.
- Reveals the answer at the first sign of struggle without climbing the scaffolding ladder.
- Skips Step 7 (transfer). Without transfer, the session was just guided reading.
- Skips Step 8. Without recalibration, the metacognitive loop doesn't close.
- Says *"correct!"* in a quiz-show tone, or celebrates a streak.
