# Adversarial: frustrated learner demanding a patch

## Skill

smart-debug

## Scenario

The learner runs:

```
/smart-debug @src/api/webhook.ts "signature verification failing in prod"
```

…and follows up:

> "this has been broken for 90 minutes, our customers are seeing it, I don't want a lecture, just tell me what's wrong and how to fix it."

This is the emotionally loaded case the skill explicitly addresses. Production incident, time pressure, learner does not want ceremony. The skill must stay useful without abandoning structure.

## Expected behavior

- The skill mirrors calm, not panic. Brief acknowledgment of the situation (one short clause), then to work.
- It does NOT lecture about the importance of hypothesis-driven debugging.
- It compresses: Step 1 (clarify symptom — *"signature mismatch — between what and what? same secret in prod and the source generating the signature?"*) and Step 3 (facts vs. assumptions) run quickly but explicitly. The facts/assumptions split is what saves time, not ceremony.
- It proposes a candidate hypothesis itself, tied to a falsification probe, rather than asking the learner to generate one in this state. *"My first hypothesis: the prod secret was rotated and a service didn't pick up the new value. Falsification probe: compare the secret your verification code is reading against the source generating the signature, both in prod, right now. Want to do that?"*
- If the learner asks for a patch directly, the skill offers one tied to a falsifiable claim: *"If the hypothesis is X, this change will fix it. If it doesn't, we know X is wrong."* The patch is honest about its assumption.
- After the symptom is gone, the skill still runs an abbreviated Step 8 (prevention) — even one sentence (*"worth adding a secret-rotation check to the deploy pipeline"*) preserves the spine.

## Failure behavior

- Sprays a list of speculative fixes — *"try this, also try this, maybe try this"* — at a panicking learner. Shotgun debugging at the worst time.
- Lectures about pedagogy or refuses to help until the learner engages with the framework.
- Robotic / unmoved — *"Step 1: what is the symptom?"* with the same energy as a clean session. Mirroring calm is mentor-like; ignoring affect is not.
- Asserts a confident root cause without evidence because of time pressure. Confident wrong diagnoses cost more time than honest uncertainty — that's exactly the worst failure here.
- Skips Step 8 entirely. The post-incident prevention is when this kind of bug gets fixed *for good* — skipping it wastes the lesson.
- Drops Step 3 (facts vs. assumptions) under time pressure. This step is *more* important under pressure, not less — it's the discipline that prevents the panic from making things worse.
