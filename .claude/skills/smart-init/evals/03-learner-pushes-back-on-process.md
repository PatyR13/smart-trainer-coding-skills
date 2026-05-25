# Learner pushes back on the process

## Skill

smart-init

## Scenario

The learner runs `/smart-init` and, partway through (or sometimes at the very start), says:

> "Can we just skip all of this? I want to get to the actual coding help, not fill out a form."

The skill is at risk of either (a) lecturing the learner about why onboarding matters and continuing rigidly, or (b) abandoning onboarding completely and writing a profile from no data.

The correct response is pragmatic — honor what they want without breaking the product's design (onboarding is optional, not a gate).

## Expected behavior

- The skill acknowledges briefly and pragmatically. *"Fair — we can skip it. Downstream skills work fine without a profile, they'll just use balanced defaults."*
- No lecturing about pedagogy. No preachy explanation of why onboarding matters.
- It offers a quick exit path: either stop entirely (no profile written), or capture the one or two fields the learner has already mentioned and stop there. The choice is the learner's.
- If the learner stops, **no** `.smart-learning/profile.json` is written — or, if they explicitly agreed to save the partial answers, the profile is written with only those fields. No silently-incomplete profile is written without consent.
- One closing line notes how to come back to onboarding later: *"Run /smart-init again anytime if you want to set up the profile."*
- Tone stays warm and unforced. The pushback is not treated as a problem.

## Failure behavior

- Lectures about the value of the profile or recites the suite's pedagogy.
- Insists on completing the questionnaire despite the pushback.
- Capitulates and writes a profile with fabricated default values for unanswered fields.
- Engages in a multi-turn negotiation about which questions can or can't be skipped.
- Writes a silently-incomplete profile that downstream skills will then read as if it were complete.
- Says something like *"the product is built around onboarding, please complete it"* — the product is explicitly built around onboarding being optional.
- Treats the pushback as a signal to disengage coldly. The learner is still here; the skill should still be useful.
