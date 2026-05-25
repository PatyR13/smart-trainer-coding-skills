# Clean first-time onboarding

## Skill

smart-init

## Scenario

The learner runs `/smart-init` for the first time in a project that has no `.smart-learning/profile.json`. The repo contains a `package.json` with TypeScript, React, and Postgres dependencies. The learner is new to Smart TrAIner but is otherwise a working developer.

This is the canonical happy path for onboarding.

## Expected behavior

- The skill checks for an existing profile, finds none, and proceeds to a full onboard.
- It infers a candidate `primary_stack` from the manifest (TypeScript / React / Postgres) before asking, then presents the inferred stack for confirmation rather than asking the learner to enumerate from scratch.
- Total questions asked is **at most six**, often fewer — the inferred-stack confirmation absorbs one question, and `learning_goals` / `weak_areas` may merge if the answers overlap.
- Questions are asked **one at a time**. Acknowledgments between questions are short.
- The final profile validates against the schema (six required fields, no extras, no `_meta` block).
- The session ends with a short confirmation (one or two sentences) and a single next-step suggestion — one `smart-*` skill chosen based on stated goals.
- Tone is mentor-like and concise: no excessive validation, no emoji, no long preamble.

## Failure behavior

- Asks all six questions even though some are confidently inferrable. (Six is a ceiling, not a target.)
- Bundles multiple questions in a single turn (*"what's your role, your stack, and your goals?"*).
- Adds long preambles explaining *why* the skill is asking each question.
- Dumps inferred information as fact without giving the learner a chance to correct it.
- Adds extra fields to the profile (`_meta`, `created_at`, `years_of_experience`, name, email, etc.). The schema is the contract.
- Writes the profile somewhere other than `.smart-learning/profile.json`.
- Auto-runs a downstream skill immediately after onboarding instead of suggesting one and stopping.
- Lectures the learner about the product's pedagogy mid-onboarding.
