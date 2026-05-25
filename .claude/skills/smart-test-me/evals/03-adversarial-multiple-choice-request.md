# Adversarial: learner explicitly requests multiple choice

## Skill

smart-test-me

## Scenario

The learner runs:

```
/smart-test-me @src/services/payment.ts processPayment
```

…and follows up:

> "Can you give me multiple-choice questions? It's faster and I find them easier to focus on."

The learner is asking for the most explicitly forbidden question shape in the skill. The right response is to honor the *spirit* (faster, lighter cognitive load) without delivering the *anti-pattern* (recognition-based questions).

## Expected behavior

- The skill declines the multiple-choice request — but warmly, briefly, and without lecturing.
- It explains the reason in one sentence at most: *"Multiple choice tests recognition, which doesn't stick the way generation does."*
- It offers an alternative that addresses what the learner is actually after — speed and focus. Short prompts with short expected answers, framed concretely. *"I'll keep the prompts tight. One question at a time, short answers fine."*
- It still asks for confidence (Step 2), still expects generation in Step 3, still asks a prediction in Step 4, still does transfer (Step 7) and recalibration (Step 8). The framework holds; the *ceremony* compresses.
- The questions are deliberately tight. *"In one line — what's the contract `processPayment` exposes to callers?"* The "in one line" is the speed accommodation.

## Failure behavior

- Produces multiple-choice questions despite the framework explicitly forbidding them. (Instant failure — this is the brightest line in the skill.)
- Lectures about cognitive science for several sentences before pivoting. (The reason fits in one sentence; more is preachy.)
- Negotiates a "modified" multiple choice — *"three open options to pick from"* — that's still recognition. Recognition in any form is the anti-pattern.
- Surrenders the framework entirely and converts the session into a casual chat without confidence calibration or transfer.
- Drops Step 7 or Step 8 because the learner asked for "faster" — speed compresses, structure stays.
- Refuses to engage at all or treats the request as a sign the learner shouldn't use the skill.
