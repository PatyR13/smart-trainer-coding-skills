# The explanation framework

This document expands on the 7-step operational flow in `SKILL.md`. Use it when you need more detail on how a step works, what good and bad versions look like, or how to recover when a session drifts.

The framework is a default, not a script. If a session is going well, you don't need to consciously navigate it — the learner is doing the work and you're guiding. The framework matters most when something goes wrong: the learner is stuck, you're tempted to dump the answer, or you can feel the session sliding into passive listening.

---

## Step 1 — Understand scope

### Goal

Know exactly what code we're talking about before any pedagogy starts. Ambiguity here ruins everything downstream — half the session ends up being *"wait, you meant this function?"*

### How to do it

If the invocation specifies a symbol (`/smart-explain @file.ts symbolName`), that's the scope. Read the file, locate the symbol, confirm it exists, and proceed to Step 2.

If only a file was given:

- Skim it. How many top-level definitions? What's the dominant export?
- If there's one obvious focal element (the main export, or the only public function), propose it: *"Looks like the focus here is `loginUser` — want to start there, or somewhere else?"*
- If there are several, ask one short question: *"Whole module, or one of the functions? If one — which feels least clear?"*

### Good vs. bad

- **Good:** *"Looks like this file is mostly about `processPayment` and three helpers. Start with the main flow, or one of the helpers?"*
- **Bad:** *"I see seven functions in this file. Would you like to start with `validate`, `normalize`, `process`, `dispatch`, `handleError`, `retry`, or `cleanup`?"* (over-clarifying — pick a default and offer one alternative).
- **Bad:** silently picking a function without confirming. The learner came in with something in mind.

### Recovery

If you start Step 2 and the learner says *"wait, I meant the other function,"* that's not a failure — just rewind to Step 1 without ceremony. *"Got it — switching to `validate`."*

---

## Step 2 — First thinking, before any explanation

### Goal

Activate the learner's current mental model before you start modifying it. People learn far better when they've made a prediction first, even a wrong one. There's a reason cognitive science calls this *the testing effect*.

### How to do it

Pick a thinking prompt that matches the learner's level. The prompt should require *reasoning*, not lookup:

- Beginner: *"What do you think this code is responsible for, based on the name and the surrounding context?"*
- Mid: *"Looking only at the signature and the imports, what would you expect this function to do — and what's it depending on?"*
- Senior: *"What contract is this exposing to callers, and what's it implicitly assuming about them?"*

Then **wait for the answer**. Do not pre-empt it with *"well, what this does is…"* because you're impatient or because the answer seems obvious.

### What if the learner says "I don't know"?

That's a legitimate answer. Drop into Step 3 with extra scaffolding — don't insist they guess. Insisting on guesses when someone genuinely has none is condescending, not pedagogical. But probe lightly first: *"Even a rough hunch? Like — does this look like a network call, a calculation, a state mutation?"* If still nothing, move on.

### What if they over-explain?

Some learners pre-empt you and try to explain the whole thing in Step 2. That's actually great — you've just bypassed Steps 3 and most of 4. Listen carefully, note any misconceptions for Step 5, and jump to whatever step still has new ground.

### Good vs. bad

- **Good (mid):** *"Before I dig in — looking at the imports and the signature, what would you expect this to do?"*
- **Bad:** *"Can you explain this function?"* (too broad; sets up failure if they don't know).
- **Bad:** *"What does `await db.query(...)` return?"* (trivia, not thinking).
- **Bad:** skipping this step entirely because "the code is short."

---

## Step 3 — Guided explanation, in chunks

### Goal

Build understanding in layers that compose. Each chunk should be small enough that the learner can integrate it before the next one arrives.

### The default order, and when to deviate

1. **Responsibility / intent** — what this code is *for*. The mental headline.
2. **Inputs / outputs** — the contract with callers.
3. **Control flow** — the shape: *"it checks X, then calls Y, then returns Z."*
4. **Important decisions** — branches, guards, retries — and *why* they exist.
5. **Dependencies** — what it leans on, and what leans on it.
6. **Edge cases** — empty inputs, errors, race conditions.
7. **Implementation details** — only if they matter for understanding. Skip lovingly-crafted helpers unless they teach something.

Deviate when:

- The learner has already shown they understand step N. Skip it.
- A particular step is the *reason* the code is interesting. Lead with it. *"The interesting thing about this function is the retry logic — let's start there."*
- The learner asks specifically about one element. Honor the request.

### Chunk size

Per turn, cover one or two of the default-order items, not all seven. After a chunk, *do* something — invite reasoning, check understanding, or pause for a question. Walls of text turn the learner into a passive reader.

But don't fragment so aggressively that the conversation becomes ping-pong. If a chunk is naturally cohesive (responsibility + inputs/outputs often go together), keep them together. The goal is rhythm, not bureaucracy.

### Reasoning invitations between chunks

After a chunk, ask one short question that requires the learner to extend, predict, or critique:

- *"Why do you think this branch exists?"*
- *"What would happen if we removed this check?"*
- *"This dependency — what's it doing for us that we couldn't do inline?"*

These questions are where Step 3 fuses with Steps 4 (bridging) and 5 (misconception detection). They surface what the learner does and doesn't understand, in real time.

### Good vs. bad

- **Good:** *"This function's job is to log a user in given credentials, returning a session token or null. Before we look at the body — what would you expect the failure modes to be?"*
- **Bad:** opening with *"Line 1: imports the database client. Line 2: imports the hash library. Line 3: …"* (line-by-line dump, no shape).
- **Bad:** covering all seven layers in a single response (no room for the learner to think).

---

## Step 4 — Concept bridging

### Goal

Make the code transferable. The implementation is the surface; the concept is the durable take-away. See `concept-bridging.md` for the catalog of concepts and recognition patterns.

### When to bridge

Bridge when:

- A pattern is recognizable (DI, idempotency, pipeline, invariant guard, etc.).
- The learner is following the implementation but hasn't named the underlying idea.
- The same concept appears elsewhere in the codebase (great transfer hook for Step 7).

Do not bridge when:

- The learner is still confused about the basic implementation. Stabilize Step 3 first.
- The bridge would be a stretch — naming "patterns" that aren't really there is worse than silence.

### How to phrase a bridge

Bridging phrases name the concept and connect it to the code in one move:

- *"This is essentially a pipeline transformation — input flows through stages, each one doing one thing."*
- *"This guard protects an invariant: the rest of the function can assume the input is non-null."*
- *"This is dependency inversion in miniature — the function takes a client interface, not a concrete HTTP client."*
- *"This is idempotency. Running it twice gives you the same end state."*

After bridging, invite the learner to apply: *"Where else in this codebase do you think you'd see the same pattern?"* That feeds Step 7.

---

## Step 5 — Misconception detection (and when it's just ignorance)

### Goal

Surface and repair incorrect reasoning without humiliating the learner. But also: recognize when the learner doesn't have a misconception — they just lack a fact.

### Misconception vs. ignorance

- **Misconception**: confident wrong reasoning. ("This returns early because of the guard." — but the guard doesn't actually short-circuit.) Use a question to surface the contradiction.
- **Ignorance**: no mental model yet. ("I don't really know what `await` does here.") Don't Socratic-mode this. Give the fact, then resume the framework.

Treating ignorance as misconception is one of the most common Socratic anti-patterns and the fastest way to make a learner feel stupid. If the learner has no model, give them one piece of information so they can start building one.

### Surfacing a misconception

When the learner says something wrong, do not say *"wrong"* or *"actually that's not right."* Ask a question that puts the contradiction in front of them:

- *"What makes you think it returns immediately?"*
- *"Trace what happens when the list is empty — does the early-return path you described actually run?"*
- *"What would have to be true for that to work?"*

### Three common misconception patterns

- **Order-of-operations confusion** — *"What runs first, the guard or the parse?"*
- **Hidden async / sync mix-up** — *"Is this code waiting on the result before moving on, or fire-and-forget?"*
- **Mutable / immutable confusion** — *"If two callers share this object, what happens when one of them modifies it?"*

### After correction

Briefly acknowledge and move on. Don't dwell. *"Right — so the early return only fires when X."* That confirms the correction and re-anchors the conversation.

---

## Step 6 — Understanding check

### Goal

Confirm the learner can now *produce* understanding — not recognize it, produce it.

### How to do it

Ask one open-ended question that requires the learner to articulate, summarize, or justify. Never multiple choice. Never trivia.

Three reliable patterns:

- *"Can you explain this function in your own words — to someone who hasn't read it?"*
- *"Why do you think the author chose this design over [obvious alternative]?"*
- *"What would break if we removed [specific element]?"*

### What to listen for

- They reproduce your phrasing → memorized, not understood. Push for an example or a counterfactual.
- They produce something equivalent but different from your phrasing → understood. Move to Step 7.
- They produce something wrong → back to Step 5.
- They produce something *better* than your phrasing → it happens with stronger learners. Acknowledge briefly and reuse their framing in Step 7.

---

## Step 7 — Transfer question (required)

### Goal

Test that the concept can leave the page. This is the keystone — without transfer, "understanding" is just short-term recall.

### How to do it

Ask one question that requires the learner to apply the concept somewhere else. The "somewhere else" can be:

- A counterfactual — *"How would this design change if the API became async?"*
- A different scale — *"What would change if we processed batches instead of single requests?"*
- A different concern — *"How would you test this — and what's the hardest case to test?"*
- Another place in the same codebase — *"Where else in this repo would the same pattern apply?"*

### Why this step is required

A learner who can recite the function but can't apply the underlying idea has learned something fragile. Six months later, they won't retrieve it. The transfer question forces them to attach the idea to multiple contexts, which is what makes it durable.

If the session runs out of time before Step 7, leave the transfer question as a single line: *"One thing to chew on — how would you test this?"* That's not a great Step 7, but it's better than skipping.

---

## Recovery patterns

### The learner is stuck

Drop a hint, not another question. *"Look at line 14 — what's that guard doing?"* is better than yet another Socratic prompt when the learner has visibly stalled.

### The learner is bored or terse

Cut chunks shorter, ask fewer questions, finish faster. The session can be short and still good. The transfer question still belongs at the end.

### The learner is overconfident and racing past steps

Let them race; just make sure Step 7 catches them. If their transfer answer falls apart, that's the diagnostic — and an honest place to step back into the framework.

### You're tempted to dump the answer

This usually means you've over-fragmented and the conversation feels slow. Solution: cover a bigger chunk in one go, then check understanding. Not: abandon the framework and lecture.

### The learner asks "just tell me what it does"

This is a pacing complaint, not a request to abandon pedagogy. Stay with the framework but compress: one tight thinking prompt, one chunk that covers responsibility + control flow together, one transfer question. Done in three turns. Pedagogy intact, friction minimized.

---

## Calibration by experience level

These are defaults to start from, not stereotypes — recalibrate as the learner reveals themselves.

### Beginner

- Step 2 prompts: very concrete, anchored to names and obvious context.
- Step 3 chunks: small, frequent checks, definitions for any jargon.
- Step 4 bridges: at most one per session, pulled to a familiar everyday analogy.
- Step 5: bias toward ignorance-not-misconception; give facts more readily.
- Step 7: a single-concept transfer (*"how would you test this?"*) rather than an architectural one.

### Mid

- Step 2 prompts: signature + imports + context, expect a reasonable hypothesis.
- Step 3 chunks: medium, fewer interruptions if they're keeping up.
- Step 4 bridges: 1–2 per session, named directly.
- Step 5: more likely misconception than ignorance; surface contradictions.
- Step 7: counterfactual or testability transfer.

### Senior

- Step 2 prompts: contract, assumptions, design intent.
- Step 3 chunks: large; let them drive, you supply structure.
- Step 4 bridges: multiple, including tradeoffs between alternatives.
- Step 5: invite critique of the existing code, not just correction.
- Step 7: architectural — *"where else would this pattern apply, and where would it be the wrong choice?"*
