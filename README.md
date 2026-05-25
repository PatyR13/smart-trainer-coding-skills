# Smart TrAIner Coding Skills

Educational Claude Code skills for learning programming through active recall, Socratic guidance, debugging reasoning, and refactoring judgment.

---

## What makes this different

Most AI coding assistants optimize for completing your task quickly. They explain code, write code, fix bugs, and refactor — *for* you.

Smart TrAIner Coding Skills optimizes for something different: **building understanding while you work**.

The skills in this repo are designed around a small set of learning principles:

- **Socratic-first guidance** — ask before explaining.
- **Active recall** — generate answers from memory, not from reading along.
- **Progressive scaffolding** — hints before answers, structure before solutions.
- **Prediction** — *what do you think this code will do?*
- **Transfer questions** — *where else does this idea apply?*
- **Confidence calibration** — *what do you actually know vs. what are you assuming?*
- **Facts vs. assumptions** — the debugging discipline that prevents most dead-ends.
- **Tradeoff reasoning** — refactoring as design judgment, not cosmetic cleanup.
- **No solution dumping** — even when asked.

The goal is not just task completion. The goal is durable understanding — knowledge that survives, transfers to new contexts, and makes you a stronger engineer.

---

## Available skills

### `/smart-init`

Optional onboarding that helps Smart TrAIner adapt to the learner. Creates a lightweight learner profile at `.smart-learning/profile.json`. Asks at most six adaptive questions and infers from the repo where possible. The profile is optional — every other skill works without it, and adapts more carefully when it exists.

### `/smart-explain`

Helps you understand code through guided questions, progressive explanation, concept bridging to programming patterns (dependency injection, idempotency, invariants, etc.), and a transfer question at the end. Anchored in real code from your repository.

```
/smart-explain @src/auth/login.ts loginUser
```

### `/smart-debug`

Teaches hypothesis-driven debugging: clarify the symptom, name your current hypothesis, separate facts from assumptions, narrow the search space, design falsification probes, find root cause, then answer the prevention question — *what would catch this class of bug next time?*

```
/smart-debug @src/api/client.ts "Cannot read properties of undefined"
```

### `/smart-test-me`

Open-ended retrieval practice. **Not** quizzes, multiple choice, or trivia. Tests understanding through generation, prediction, and transfer — with confidence calibration before and after to surface the illusion of competence.

```
/smart-test-me async-await
```

### `/smart-refactor`

Refactoring as design reasoning. Asks what the existing code might be optimizing for *before* identifying smells, walks through tradeoffs explicitly, compares alternatives, and treats *"leave it alone — here's why"* as a legitimate session outcome.

```
/smart-refactor @src/services/payment.ts processPayment
```

---

## What a session feels like

Short stylized previews of what each skill's interaction model looks like in practice. Real sessions vary, but these capture the shape.

### `/smart-explain`

> **`/smart-explain @src/auth/login.ts loginUser`**
>
> Before we look at the body — what do you think this function is responsible for, just from the name and the signature?
>
> *(learner answers)*
>
> Looking only at the inputs, what would you expect the failure modes to be?
>
> *(learner answers)*
>
> Right. Now — why do you think this validation block exists at the top? What would happen without it?

### `/smart-debug`

> **`/smart-debug @src/api/client.ts "Cannot read properties of undefined"`**
>
> Let's anchor on what we actually know. Do you have the exact stack trace? Repro steps?
>
> *(learner answers)*
>
> What's your current best hypothesis?
>
> *(learner answers)*
>
> Useful one. What would falsify it — what would we expect to see if it were wrong?

### `/smart-test-me`

> **`/smart-test-me async-await`**
>
> Quick anchor: how confident are you that you understand async/await? Low, medium, or high?
>
> *(learner answers)*
>
> Explain it in your own words — as if to a colleague who only knows callbacks.
>
> *(learner answers)*
>
> Prediction time: what happens to the rest of the function while it's awaiting? And what if the awaited promise rejects?
>
> *(...a few turns later...)*
>
> Has your confidence shifted from where it was at the start?

### `/smart-refactor`

> **`/smart-refactor @src/services/payment.ts processPayment`**
>
> Before we look for problems — what do you think this code is optimized for? Is there a constraint that might explain the shape?
>
> *(learner answers)*
>
> Fair. Now — what feels uncomfortable to you about it? If you could improve one thing first, what would it be?
>
> *(learner answers)*
>
> Let's stay honest about cost. If we extracted that block into its own function, what improves — and what specifically gets more complex?

---

## Installation

### Prerequisites

- [Claude Code](https://claude.ai/code) installed locally
- Git installed
- A repository where you want to use the skills

### Option A — Install the skills into a target project

Clone this repository alongside your target project, then copy the skills into it:

```bash
git clone https://github.com/PatyR13/smart-trainer-coding-skills.git
cd your-target-project
mkdir -p .claude/skills .claude/shared
cp -R ../smart-trainer-coding-skills/.claude/skills/* .claude/skills/
cp -R ../smart-trainer-coding-skills/.claude/shared/* .claude/shared/
```

This makes the Smart TrAIner skills available inside your project's Claude Code environment without changing anything else in your repo.

### Option B — Work directly inside this repository

```bash
git clone https://github.com/PatyR13/smart-trainer-coding-skills.git
cd smart-trainer-coding-skills
claude
```

Once Claude Code is running in either setup, run:

```
/skills
```

to verify the Smart TrAIner skills are visible.

Windows users can perform the same setup by manually copying the `.claude/skills/` and `.claude/shared/` directories into the target repository — the file layout is what matters, not the copy command.

Installation is intentionally simple and file-based for now. A dedicated installer CLI may come later, but the current setup keeps everything transparent and easy to inspect.

---

## How skill invocation works

These skills are designed primarily for **explicit slash-command invocation**. You point each one at a target — usually a file reference and (optionally) a symbol name or error string:

```
/smart-debug @src/api/client.ts "Cannot read properties of undefined"
```

Claude Code may also auto-select a skill based on its own internal heuristics, but auto-invocation should not be treated as guaranteed. If you want a Smart TrAIner skill to drive the interaction, run it explicitly. The recommended interaction model is explicit invocation — the slash command is the contract.

---

## Usage examples

```
/smart-init
/smart-explain @src/auth/login.ts loginUser
/smart-debug @src/api/client.ts "Cannot read properties of undefined"
/smart-test-me async-await
/smart-refactor @src/services/payment.ts processPayment
```

### Recommended first flow

When approaching new code or a new concept:

```
/smart-init                                    # one-time, optional
/smart-explain @path/to/the/code.ts <symbol>   # build a mental model
/smart-test-me <concept-or-symbol>             # stress-test it
```

When working on an actual bug:

```
/smart-debug @path/to/file.ts "<error or symptom>"
```

When considering whether to clean up some code:

```
/smart-refactor @path/to/file.ts <symbol>
```

---

## Evaluation scenarios

Each core teaching skill ships with lightweight documentation-based eval scenarios under:

```
.claude/skills/<skill-name>/evals/
```

These are **not executable tests**. They are behavioral contracts in markdown — descriptions of what good and bad skill behavior looks like in concrete scenarios (easy cases, ambiguous cases, adversarial cases). They serve as:

- A reference for what the skill is contractually supposed to do.
- A guide for spotting regressions or edge-case failures.
- A foundation that an automated eval harness could be layered on top of later.

---

## Repository structure

```
.claude/
  shared/
    pedagogical-principles.md
    learner-profile-schema.json
  skills/
    smart-init/
    smart-explain/
    smart-debug/
    smart-test-me/
    smart-refactor/
```

Each skill directory contains a `SKILL.md` (the skill definition Claude Code reads), a `references/` folder with deeper pedagogical material, and — for the four core teaching skills — an `evals/` folder with behavioral contracts.

---

## Philosophy

> **AI should not think for you. AI should help you think better.**

This is the core idea behind Smart TrAIner. Modern AI coding assistants are powerful, but they create a risk: shipping code faster while learning less, growing more dependent on tools and less confident in your own reasoning. These skills are an attempt to push the other way — to treat AI as a learning partner, not a solution vending machine.

The pedagogical principles every skill in this repo honors are documented in [`.claude/shared/pedagogical-principles.md`](.claude/shared/pedagogical-principles.md).

---

## Roadmap

The current focus is **polishing the existing five skills based on real usage**, not adding more.

Possible future ideas — *not currently in scope*:

- `smart-trace` — understanding execution flow through a codebase
- `smart-review` — educational code review
- `smart-architecture` — system design mentoring

These are exploratory directions, not promises. Real-world feedback on the existing five will shape what comes next.

---

## Contributing

Issues, suggestions, and pull requests are welcome. If you've used the skills and found a behavior that violates the pedagogical principles — or one that breaks productive flow — that's a particularly useful contribution.

If you care about AI-assisted learning and developer education, contributions are encouraged.

---

## License

MIT. See [LICENSE](LICENSE).

---

## About Smart TrAIner

Smart TrAIner is a broader learning project focused on using AI as a learning partner, not a replacement for thinking. This repository is the open-source **coding-learning extension** of that vision — applying the same principles to how developers learn while writing real code.

Learn more at [smarttrainerai.app](https://smarttrainerai.app/).
