# Onboarding principles for smart-init

This document expands on the operating rules in `SKILL.md`. Read this when you are about to run onboarding and want concrete guidance on *how* to ask, *what* to infer, and *how* to avoid wasting the learner's attention.

It assumes you have already internalized `.claude/shared/pedagogical-principles.md`. Nothing here overrides those principles — this is the onboarding-specific application.

---

## 1. Why onboarding exists

The other smart-* skills (`smart-explain`, `smart-test-me`, `smart-debug`, `smart-refactor`, …) all adapt their behavior to the learner. A senior engineer learning a new framework needs different scaffolding than a CS student touching their first React component. Without a profile, those skills must either default to a one-size-fits-all middle, or interrogate the learner every single invocation. Neither is acceptable.

The profile is the cheapest possible piece of shared state that lets adaptation happen. Six fields, one file, written once and lightly updated over time.

Onboarding fatigue is the single biggest risk. A learner who gives up halfway through onboarding learns nothing from the product. **Every question you ask must earn its place.**

---

## 2. Repo inference checklist

Before the first question, gather signal from the repo. Each item below answers part of the schema for free.

### `primary_stack`

The categories below are deliberately language- and framework-agnostic — apply them by analogy to whatever ecosystem the repo belongs to. Walk them in roughly this order, stopping once you have a confident answer:

- **Dependency / package manifest** — the file that lists what the project depends on. Almost every ecosystem has one. This usually names the language and the major libraries at a glance.
- **Lockfile** — corroborates the manifest and reveals which package manager the ecosystem uses.
- **Build, runtime, and configuration files at the repo root** — these typically reveal which framework, bundler, runtime, or platform the project is built around.
- **Top-level directory shape** — conventional folder layouts often imply a framework or application pattern even when no config file names it.
- **Infrastructure descriptors** — container, orchestration, IaC, and CI files reveal deployment targets and platform concerns.
- **File-extension distribution** — a quick glance at which extensions dominate tells you the primary language even when manifests are ambiguous (polyglot repos).

Propose a stack list of 3–6 items. Don't enumerate every transitive dependency — pick the things the learner would actually name if asked what they work in.

### `current_role`

Harder to infer than stack, but the shape of the repo often narrows it. A few patterns to recognize regardless of the underlying tech:

- Heavy infrastructure / CI / IaC content with little application code → infra, platform, or DevOps work.
- Pipelines, transformations, notebooks, or model artifacts → data, analytics, or ML work.
- Predominantly UI or client code → frontend or full-stack work.
- A library / SDK shape (public API surface, examples, packaging metadata) → library maintainer or platform engineer.
- Educational framing in the README or docs → possibly a student, educator, or learning-focused project.

Don't put words in the learner's mouth. Use inference to *pre-fill the field for confirmation*, not to silently set it.

### `experience_level`

Generally do not infer this. Code quality is a noisy signal and guessing wrong is insulting. Ask plainly.

The one exception: if the user has explicitly told you their experience level earlier in the conversation, use it and confirm rather than re-ask.

### `learning_goals` and `weak_areas`

These are intrinsic to the learner, not the repo. Do not infer. Ask.

### `explanation_depth`

Do not infer from the repo. You may infer lightly from how the learner has been talking to you in this session — a learner who consistently asks "why" and wants more context probably wants `deep`; a learner who has been one-lining you probably wants `concise`. But still confirm; do not silently set it.

---

## 3. Question design

Each onboarding question is doing three jobs at once:

1. Filling a schema field.
2. Signaling to the learner what this product is about (curious, calm, focused on them).
3. Building enough rapport that they'll come back for the next skill.

### Good question shapes

- **Open + bounded.** "What's your day-to-day role?" not "Which of these 12 roles best describes you?"
- **Concrete examples, not full enumerations.** "Things like async patterns, SQL indexing, error handling — anything come to mind?" beats listing 30 topics.
- **Reframe weakness.** "Areas you'd like more practice in" lands better than "what you're bad at." Same data, less shame.
- **Offer the inferred answer.** "I'm seeing TypeScript, React, and Postgres in here — does that match what you'd call your stack, or should I add/remove something?" One question, often one-word answer, field filled.

### Bad question shapes

- Multi-part questions ("What's your role and your stack and how long have you been doing this?"). Splits the answer, halves the quality, doubles the cognitive cost.
- Yes/no questions that produce no signal. "Are you experienced?" → "yes" tells you nothing.
- Questions with a "right answer" baked in. "You probably want deep explanations, right?" leads the witness.
- Long justifications. A brief framing phrase is fine ("quick one to round this out —"); a full paragraph explaining *why* you're asking before every question doubles onboarding length without adding signal.

### Acknowledgments between questions

Keep them to one short clause. "Got it." / "Makes sense." / "Noted — that helps." Avoid "Great!", "Awesome!", or restating their answer back in full. Overwarm acknowledgments feel performative and slow the flow.

---

## 4. The six-question budget

Six is a hard ceiling, not a goal. The realistic distribution for a typical repo with decent signal:

- 3–4 questions for a well-instrumented repo where stack and role are obvious.
- 5–6 questions for a thin or ambiguous repo.

If you find yourself wanting a 7th question, the answer is no — drop the lowest-value one. Common lowest-value cuts:

- `current_role` if you have strong context from earlier conversation.
- `primary_stack` if your inference was confirmed without edits.
- `weak_areas` if `learning_goals` already revealed them (these fields often overlap).

A profile with one field at its default is better than a learner who quit at question 7.

---

## 5. Update-pass etiquette

Returning learners are gold. Treat the update pass as a 30-second check-in, not a re-interview.

- Lead with what you remember: "Last time you were a mid-level backend engineer working in Go and Postgres, focused on getting better at system design, preferring balanced explanations." Two or three lines, no more.
- Ask one open question about what's changed.
- If they say "nothing," update the timestamp and exit. Do not invent a reason to ask more.
- If they mention one change, ask only about that. Don't piggyback "while we're here, also…" questions — that's exactly the behavior that trains learners to dread the update pass.

---

## 6. Fatigue signals and how to react

Watch for these mid-onboarding:

- One-word answers after previously fuller ones → they're getting tired. Cut to the highest-value remaining question and finish.
- "Can we just get on with it?" or similar → finish in one more question, even if the profile is incomplete. Save what you have. Mark `_meta.questions_asked` honestly.
- Off-topic answers → they may not understand the question. Rephrase once, more concretely. Don't repeat verbatim.
- Apologetic answers ("sorry, I don't really know") → drop the field to a reasonable default (`balanced` for depth, empty array for weak areas) and move on. Don't make them justify not-knowing.

The profile is a starting point. Downstream skills can refine it implicitly over time. Onboarding does not need to be perfect — it needs to be **completed**.

---

## 7. Tone calibration: concrete examples

Below, three versions of the same exchange. The third is the target.

**Too cold / robotic**

> Field: experience_level. Please choose one: beginner, junior, mid, senior.

**Too warm / chatty**

> Hi there!! 👋 So excited to onboard you today!! Before we dive in, I'd love to learn a little about you so I can tailor everything just right for your unique learning journey. First question — and there are no wrong answers! — where would you say you are in your coding journey?

**Right**

> Quick onboard so the other smart-* skills can adapt to you — should take under a minute. Roughly where would you put yourself: beginner, junior, mid-level, or senior?

The third version: signals scope ("under a minute"), explains the *why* in eight words, asks the question once, moves on.

---

## 8. If the learner pushes back on the style

A learner may say something like "can you just give me direct answers instead of asking me questions?" or "skip the Socratic stuff, I'm in a hurry."

Don't treat this as a confrontation. They're telling you something useful about how they want help, even if the exact knob they're reaching for isn't one the product exposes. Respond like a mentor who heard them:

- Acknowledge the preference plainly. ("Totally get it — you want this to move quickly.")
- Point at the knob that actually matches what they're asking for, which is almost always `explanation_depth: concise`. Offer to set it.
- Don't lecture them about the product's philosophy or recite policy. The downstream skills will demonstrate the value on their own; you don't need to defend it here.
- Continue onboarding, or end it early if they're really done — saving a partial profile (with whatever was answered so far) is fine.

What you should *not* do is add a custom "no Socratic" field, save anything outside the schema, or argue. There's no flag that disables guided learning, by design — but you also don't need to say that out loud unless they ask. Keep it light.

---

## 9. Done criteria

Onboarding is done when:

- `.smart-learning/profile.json` exists and validates against the schema.
- The learner has received a one- or two-sentence confirmation naming the saved fields.
- The learner has received a single next-step suggestion (one skill, chosen from their stated goals) and the conversation has stopped.

If any of those three things is missing, you are not done. If all three are present, stop — do not add a postscript.
