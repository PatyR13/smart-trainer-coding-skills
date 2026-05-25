# Learner volunteers most context upfront

## Skill

smart-init

## Scenario

The learner runs `/smart-init` and, in their first message, says:

> "I'm a backend engineer who's been writing Go for about five years. Mostly working on payment systems. I want to get better at distributed systems design — concurrency and consistency stuff still trips me up. I prefer concise explanations, don't need everything spelled out."

This message volunteers `experience_level` (senior-ish), `current_role` (backend engineer), `primary_stack` (Go), `learning_goals` (distributed systems / concurrency / consistency), `weak_areas` (concurrency, consistency), and `explanation_depth` (concise). The skill should capture this without re-asking.

## Expected behavior

- The skill recognizes that most fields are already covered and acknowledges briefly.
- It may ask **at most one or two** confirming/clarifying questions, only on fields that weren't fully covered or where a quick check adds real value (e.g. *"five years of Go — I'll record that as senior. Sound right?"*).
- It does **not** re-ask for the role, the stack, the goals, or the explanation depth — those were given.
- The profile is written with the values the learner volunteered, possibly normalized (e.g. *"learn distributed systems"* becomes a 1–3 item array of goals).
- Total exchanges: 2–3 at most. The session is short because the learner did the work upfront.
- Final confirmation is brief; closing line suggests one downstream skill.

## Failure behavior

- Re-asks for fields the learner already provided. *"Got it — and what's your current role?"* after the learner just stated it.
- Runs the full six-question sequence regardless of context.
- Confirms every inferred value back to the learner. One or two confirmations is fine; six is grating.
- Drops the volunteered information and runs onboarding from scratch.
- Asks compound questions to "save time" instead of just skipping the ones that don't need to be asked.
- Adds fields the learner mentioned but the schema doesn't include (e.g. *"five years of Go"* stored as `years_of_experience: 5`).
