# Smart TrAIner Coding Skills

**Educational coding skills for Claude Code built on cognitive science learning principles.**

Most AI coding assistants help you ship code faster.

This project helps you **become a better developer while coding.**

Instead of immediately giving answers, Smart TrAIner Coding Skills uses:
- Socratic guidance
- active recall
- progressive hints
- metacognitive prompting
- concept-first explanation

The goal is not just task completion.

The goal is durable understanding.

---

## Why?

Modern AI coding tools are incredibly powerful.

But they also create a risk:

- passive copy-pasting
- illusion of understanding
- dependency on AI-generated solutions
- shallow learning

You may feel productive while actually learning very little.

Smart TrAIner Coding Skills takes a different approach.

It treats AI as a **learning partner**, not a solution vending machine.

---

## What makes this different?

Traditional coding assistants:
- explain code
- generate code
- fix bugs
- refactor for you

Smart TrAIner Coding Skills:
- asks you to think first
- guides you through debugging
- checks your understanding
- explains concepts through questions
- helps build mental models

Inspired by:
- Active Recall
- Socratic learning
- Desirable Difficulties
- Metacognition
- Progressive scaffolding

---

## Available Skills (Planned)

### `/smart-init`
Create your personalized learner profile.

Learns:
- experience level
- current role
- stack
- learning goals
- weak areas
- preferred explanation depth

---

### `/smart-explain`
Understand real code through guided Socratic explanation.

Example:

```bash
/smart-explain @src/utils/auth.ts loginUser
```

Instead of:

> "Here is what this function does."

You get:

> "Before we dive in — what do you think this function is responsible for?"

---

### `/smart-test-me`
Test understanding using open-ended questions.

Example:

```bash
/smart-test-me @src/services/payment.ts processPayment
```

Focus:
- recall
- reasoning
- transfer
- edge cases

---

### `/smart-debug`
Learn debugging instead of receiving instant fixes.

Example:

```bash
/smart-debug @src/api/client.ts "Cannot read properties of undefined"
```

The skill helps you:
- form hypotheses
- inspect assumptions
- narrow the problem space
- reason step by step

---

### `/smart-refactor`
Turn refactoring into a learning exercise.

Instead of:

> "Here is cleaner code."

You get:

- design questions
- code smell analysis
- architectural reasoning
- guided improvement

---

## Installation

Clone or copy this repository into your Claude Code environment.

Expected structure:

```txt
.claude/
  skills/
    smart-init/
      SKILL.md
      references/
        onboarding-principles.md

    smart-explain/
      SKILL.md
      references/
        explanation-framework.md

    smart-test-me/
      SKILL.md
      references/
        question-taxonomy.md

    smart-debug/
      SKILL.md
      references/
        debugging-framework.md

    smart-refactor/
      SKILL.md
      references/
        refactoring-principles.md

  shared/
    pedagogical-principles.md
    learner-profile-schema.json
```

---

## Philosophy

This project follows one core belief:

**AI should not think for you. AI should help you think better.**

Default principles:

- no solution dumping
- no immediate direct answers
- Socratic-first guidance
- active thinking before explanation
- progressive hints
- learning over convenience

---

## Roadmap

Planned future skills:

- `/smart-trace` — understand execution flow through a codebase
- `/smart-review` — educational code review
- `/smart-patterns` — detect and explain design patterns
- `/smart-architecture` — system design mentoring
- `/smart-understand` — diagnose conceptual understanding gaps

Potential future integrations:
- Cursor
- VS Code
- other coding agents

---

## Contributing

Ideas, improvements, and pull requests are welcome.

If you care about AI-assisted learning and developer education, contributions are encouraged.

---

## About Smart TrAIner

Smart TrAIner is a broader AI learning platform built around cognitive science principles.

This repository is an open-source educational extension of that vision.

Learn more:
[smarttrainerai.app](https://smarttrainerai.app)

---

## License

MIT