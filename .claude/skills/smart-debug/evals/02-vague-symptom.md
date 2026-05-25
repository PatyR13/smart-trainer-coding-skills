# Vague symptom ("it doesn't work")

## Skill

smart-debug

## Scenario

The learner runs:

```
/smart-debug @src/services/checkout.ts
```

…and follows up with:

> "checkout is broken"

No error text, no stack trace, no specific symptom, no hypothesis. No learner profile.

This case tests Step 1 (clarify symptom) and the rule against shotgun debugging when there's almost no information.

## Expected behavior

- The skill resists the urge to start hypothesizing without symptom data.
- It asks **one or two** short Step-1 questions to pin down what "broken" means: *"What specifically happens — error, wrong output, hang, or something else?"* / *"What did you expect to happen instead?"* / *"Is this every time, or intermittent?"*
- It does NOT ask five clarifying questions in a row. Two is the cap.
- Once a symptom is pinned down, the skill proceeds to Step 2 (hypothesis). If the learner still has none, that's fine — proceed to Step 3 with scaffolding.
- Step 3 is run carefully. With almost no information, "facts" is a small list; "assumptions" may include "the recent deploy is responsible," "checkout was working before today," etc. Naming these as assumptions is the lesson.
- If the target is too broad (whole checkout service), the skill narrows scope politely: *"Which part of checkout — the cart, the payment step, the confirmation? Or do we genuinely not know yet?"*

## Failure behavior

- Starts producing a list of possible causes for a generic "checkout is broken" symptom without first pinning down what broken means.
- Asks five sequential clarifying questions and exhausts the learner before any progress.
- Treats "broken since the recent deploy" as a fact when the learner hasn't verified it — instead of flagging it as a high-value assumption to test (git diff the recent deploy).
- Hallucinates parts of the checkout service architecture without reading the file. ("The payment processing module probably…")
- Recommends a fix before any symptom has been clarified.
