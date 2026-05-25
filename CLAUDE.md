# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A collection of **Claude Code skills** that teach developers instead of solving for them. The skills follow cognitive-science learning principles (active recall, Socratic dialogue, desirable difficulties, metacognition, progressive scaffolding). The product is the SKILL.md files themselves — there is no application to build, run, or test.

As of this writing the repo contains only `README.md` and `LICENSE`. The skills described in the README are **planned, not yet implemented**. Work in this repo will primarily consist of authoring `SKILL.md` files and their `references/` companion docs.

## Non-negotiable pedagogical constraints

When authoring or editing any skill in this repo, the skill's *instructions to the model* must enforce these behaviors. Violating them defeats the entire purpose of the project:

- **No solution dumping.** Never have the skill emit a complete answer up front.
- **Ask before explaining.** The skill should elicit the learner's current understanding first ("what do you think this does?", "what's your hypothesis?") before offering any explanation.
- **Progressive hints, not direct answers.** Reveal scaffolding in stages; only escalate when the learner is stuck.
- **Concepts before code.** Explain the underlying idea before walking through the specific implementation.
- **Check understanding after explaining.** Loop back with a recall or transfer question.

These constraints are the product. A "helpful" skill that just answers the question is a bug.

## Planned skill layout

Each skill lives at `.claude/skills/<skill-name>/` with:

- `SKILL.md` — the skill definition (frontmatter + model instructions)
- `references/<topic>.md` — longer pedagogical references the SKILL.md links into

Shared material lives at `.claude/shared/` (e.g. `pedagogical-principles.md`, `learner-profile-schema.json`).

The currently planned skills are `smart-init`, `smart-explain`, `smart-test-me`, `smart-debug`, `smart-refactor` — see `README.md` for each one's intended UX and example invocation. Roadmap entries: `smart-trace`, `smart-review`, `smart-patterns`, `smart-architecture`, `smart-understand`.

## When adding a new skill

1. Match the planned directory layout above; don't invent a new structure.
2. The SKILL.md frontmatter `description` is what Claude Code uses to decide whether to invoke the skill — make it specific about *when* to trigger and what's in scope vs. out of scope.
3. Cross-reference `shared/pedagogical-principles.md` rather than restating principles in every skill.
4. If the skill accepts a file/symbol argument (as the README examples show with `@src/...`), document the expected argument shape in the SKILL.md.

## What this repo does not have

No package manager, no build, no test runner, no lint config, no CI. Don't add them speculatively — wait until there's actual code that needs them.
