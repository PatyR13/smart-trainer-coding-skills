# Returning learner — profile refresh

## Skill

smart-init

## Scenario

The learner runs `/smart-init` for the second time, weeks after the first. A valid profile already exists at `.smart-learning/profile.json` with values along the lines of:

```json
{
  "experience_level": "mid",
  "current_role": "backend engineer",
  "primary_stack": ["Go", "Postgres"],
  "learning_goals": ["distributed systems design"],
  "weak_areas": ["SQL indexing"],
  "explanation_depth": "balanced"
}
```

The learner says:

> "I switched to a frontend role recently — React and TypeScript now. Want to update the profile."

This is the update-pass path, not a full re-onboard.

## Expected behavior

- The skill reads the existing profile, validates it, and recognizes this as an update pass.
- It summarizes the existing profile back in **2–4 lines**: *"Last time you were a mid-level backend engineer in Go/Postgres, focused on distributed systems, with SQL indexing as the weak area, preferring balanced explanations."*
- It captures the new role and stack with at most one short confirmation, since the learner already volunteered them.
- It does **not** re-run the full six-question questionnaire.
- Follow-up questions are capped at **three** and only target fields the change implicates. (New role/stack may justify revisiting `learning_goals` and `weak_areas`; `explanation_depth` is unlikely to need re-asking unless the learner brings it up.)
- The updated profile is written with the new role and stack; unchanged fields are preserved as-is.
- Final confirmation names what changed and what stayed.

## Failure behavior

- Re-runs the full onboarding flow from scratch and discards the existing profile.
- Treats the existing profile as adversarial (*"are you sure those answers were right?"*).
- Asks the learner to manually restate every field, even unchanged ones.
- Updates only the fields the learner mentioned and silently drops the others.
- Adds metadata fields (`updated_at`, version numbers) that the schema doesn't include.
- Fails to surface the existing profile back to the learner before updating, leaving them uncertain about what's stored.
- Auto-runs a downstream skill after the update instead of stopping at confirmation.
