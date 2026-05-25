# The debugging framework

This document expands on the 8-step operational flow in `SKILL.md`. Use it when you need more depth on how a step works, what good and bad versions look like, or how to recover when a debugging session goes sideways.

The framework is hypothesis-driven debugging written down. It's the difference between *"I'll try a few things"* and *"I have a hypothesis, here's what would falsify it, here's the cheapest probe."* Most engineers can do the first naturally. The second is teachable, and almost no one teaches it explicitly.

---

## The core habit: facts vs. assumptions

If you take only one thing from this document, take this:

**Debugging failures are usually downstream of treating an assumption as a fact.**

The bug is "weird" not because the system is mysterious, but because somewhere the learner's mental model says *"this is definitely happening"* when in fact *"this hasn't been checked."* The structured framework is mostly machinery for forcing that distinction into the open.

Every Step 3 fact-vs-assumption pass is the skill's most important moment. Don't rush it.

---

## Step 1 — Clarify the symptom

### Goal

Pin down what is actually being observed, separately from why. The shape of the symptom narrows everything downstream.

### Symptom categories worth recognizing

- **Crash / exception** — process or function terminates with an error.
- **Wrong output** — code returns or produces something it shouldn't.
- **Missing side effect** — code "ran" but the expected effect didn't happen.
- **Flaky / intermittent** — sometimes works, sometimes doesn't.
- **Hang / infinite loop / timeout** — never returns.
- **Race condition** — outcome depends on timing between concurrent operations.
- **Stale state** — data observed is older than expected.
- **Performance degradation** — works, but slowly.
- **Memory / resource leak** — usage grows over time.

Each category steers Step 4 toward a different partition of the search space. *Flaky* points at async/timing/race; *missing side effect* points at I/O / transactional boundaries; *wrong output* points at logic or input.

### Pragmatic adjustment

If the learner has handed you exact error text, a stack trace, and a deterministic repro, Step 1 is done. Don't re-interrogate. Acknowledge and move on. The point of Step 1 is to *get* clarity, not to perform the ritual of asking for it.

### Good vs. bad

- **Good:** *"This is intermittent? Same input, sometimes pass / sometimes fail?"* (one question, narrows to async/timing).
- **Bad:** *"Tell me everything about what's happening."* (too broad; learner doesn't know what's relevant).
- **Bad:** skipping Step 1 because the learner sounded confident and you assume the symptom is clear — half the time it isn't.

---

## Step 2 — Elicit current hypothesis

### Goal

Surface the learner's working theory before introducing your own. This is partly for diagnostic value (you learn what their mental model looks like) and partly to keep them in the driver's seat.

### How to ask

- *"What's your current best guess?"*
- *"If you had to bet, where would you look first?"*
- *"What changed recently — is there a candidate?"*

### When the learner has no hypothesis

That's fine. Don't insist. Move to Step 3 with stronger scaffolding — sometimes you need facts on the table before any hypothesis is possible. Pretending to guess when you genuinely can't is bad debugging hygiene.

### When the learner has multiple

Great — list them, then attack the cheapest-to-falsify one first in Step 5.

### When the hypothesis is obviously wrong

Don't say so. The Step 5 falsification machinery will surface it. Walking someone through *"what would we expect to see if this were true? OK, do we see that?"* is far more pedagogically valuable than *"actually that's not it."*

---

## Step 3 — Evidence inventory (the critical step)

### Goal

Separate what's known from what's assumed. Build a shared list of facts.

### How to run it

Ask: *"What do we actually know so far?"*

As the learner answers, mentally (and sometimes visibly) sort each item:

- **Fact** — directly observed in this session: error text, log line, test output, code seen with your own eyes.
- **Assumption** — believed but not verified in this session: *"the user was logged in,"* *"that endpoint returned 200,"* *"the deploy didn't change anything in this path."*

When you spot an assumption being treated as fact, name it: *"That's an assumption — have we actually checked it, or are we treating it as true?"*

### Common assumption traps

- *"It worked yesterday"* — was that the exact same code, environment, and input?
- *"That branch never runs"* — has the learner actually traced it, or are they reasoning from the code?
- *"It must be X"* — said with confidence about something not verified.
- *"The deploy didn't change anything"* — almost always worth checking.
- *"Tests pass locally"* — different environment, different state.

### The verification ladder

For each assumption, there's a cheapest available probe. Move down the ladder until something is actually verified:

1. **Read the code again, more carefully.** Free.
2. **Read the logs / output we already have.** Free.
3. **Add a single log line and reproduce.** Cheap.
4. **Write a one-off probe (a debug call, a tiny script).** Cheap.
5. **Step through with a debugger.** Medium.
6. **Bisect / git-archeology / instrument harder.** Expensive.

Push for the cheapest probe that would actually distinguish hypotheses. This habit alone is worth the whole skill.

---

## Step 4 — Narrow the search space

### Goal

Convert "the bug is somewhere in the system" into "the bug is in one of three places."

### Partitioning, not enumerating

Don't list 12 things that could be wrong. List 3–4 *categories* that could be wrong:

- *"For wrong output, the cause is usually one of: bad input, bad logic, or bad assumption about a dependency. Which of those has the most support so far?"*
- *"For a flaky test, it's usually: timing-dependent code, shared state between tests, or env differences. Which feels most likely given what we've seen?"*

Each category corresponds to a different *probe* in Step 5. Three partitions × three probes is manageable. Twelve possibilities × twelve probes is paralysis.

See `failure-patterns.md` for the catalog of common bug shapes, each with a partition it implies.

### When you can't partition cleanly

Sometimes the symptom is genuinely uncategorizable until more evidence comes in. That's fine — say so, and use Step 5 to design a probe that *itself* narrows the search.

---

## Step 5 — Hypothesis testing (the keystone)

### Goal

Convert *"I think it's X"* into *"X predicts Y; do we see Y?"* — and prefer probes that can falsify cheaply.

### The three falsifiability questions

For every hypothesis:

1. *"What would we observe if this were true?"*
2. *"What would falsify it — what would we see if it were wrong?"*
3. *"What's the cheapest probe that would tell us either way?"*

### Why falsification, not confirmation

Confirmation bias is the debugging killer. If you go looking for evidence that your hypothesis is right, you'll find it — that's how brains work. If you go looking for evidence it's *wrong*, you either rule it out (one less candidate) or you find a contradiction (a real signal). Both are productive.

Strong debuggers ask *"what would prove this wrong?"* before asking *"how do I confirm it?"*

### Worked example

Hypothesis: *"The login fails because the password hash comparison returns false when it should return true."*

- Predicts: if we log both the stored hash and the computed hash, they should differ when login fails.
- Falsifies: if they're identical at the comparison point, the bug isn't in hash comparison — it's upstream (loading the user) or downstream (the function returning the wrong value).
- Cheap probe: one log line at the comparison, one reproduction.

Three minutes of work decides between two large branches of the search tree.

### Bad shapes

- **Untestable hypothesis:** *"Maybe it's a weird race condition somewhere."* This doesn't predict anything specific. Refine until it does.
- **Confirmation-only probe:** *"Let's check whether the cache is being hit."* What does a hit prove? What does a miss prove? If the answer is "I don't know," the probe is decorative.
- **Speculative scatter:** *"It could be A, B, C, D, or E."* Pick one and design a falsification probe — or pick a probe that distinguishes a partition (Step 4).

---

## Step 6 — Code-guided investigation

### Goal

Anchor reasoning in code the learner and you can both see, not in mental models.

### What to read

- The implicated function and its immediate callers / callees.
- Imports (especially indirections — wrappers, factories, DI containers).
- Related tests, both failing and passing — passing tests are often more informative than failing ones because they tell you what the code *does* handle correctly.
- Config that might differ between environments.
- Recent git history on the file (`git log -p` is sometimes the whole investigation).

### Two strict rules

- **Don't invent architecture.** If you can't see how X calls Y, say so. *"I can't tell from here whether this is going through the retry wrapper — want to check?"* is far better than confidently asserting it does.
- **Don't fabricate causes.** If the evidence doesn't converge on a single cause, say *"we don't know yet"* and propose the next probe. Confident wrong diagnoses cost more time than honest uncertainty.

### Pacing

Pull in code as needed, not preemptively. Reading half the repo "for context" before forming a hypothesis is the *opposite* of structured debugging — it's the haystack approach with extra steps.

---

## Step 7 — Root cause articulation

### Goal

The learner must produce — in their own words — what the root cause was, why the symptom appeared, and what assumption turned out to be wrong.

### Three questions to choose from

- *"Can you explain the root cause in your own words?"*
- *"What assumption turned out to be wrong?"*
- *"Why did the symptom appear instead of the expected behavior — what was the chain?"*

### Listen for

- **Crisp causal chain** → the learner understands.
- **Hand-waving** → there's still a misconception. Back to Step 5 with a sharper probe.
- **Re-citing the symptom** → not understanding, just recall. Push for the *mechanism*.
- **"It just works now"** → not acceptable. Some change made the symptom disappear; the question is *why*.

### Why this step is non-negotiable

A learner who can fix a bug but can't explain it will produce the same bug again, or a sibling of it. Articulation is what makes the lesson portable.

---

## Step 8 — Prevention thinking (required)

### Goal

Turn the bug into something the codebase, the test suite, or the team's habits actively prevent going forward.

### The four prevention archetypes

Most prevention answers fall into one of four buckets. Bridge to the right one based on the bug shape:

1. **Test** — a new automated check that would fail when the bug exists.
   - Best for: logic bugs, regressions, bugs with deterministic repro.
   - Push for specificity: not *"add a test"* but *"add a test for what property?"*

2. **Guard / assertion** — runtime check that catches the bad state at its source instead of letting it propagate.
   - Best for: invariant violations, contract mismatches, *"this should never happen but it did"* bugs.
   - Trade-off: cost of the check vs. cost of debugging without it.

3. **Design change** — restructuring so the bug becomes impossible (or much harder) to introduce.
   - Best for: bugs that recur, bugs that hit a whole class of code, bugs caused by accidental complexity.
   - Examples: making mutable state immutable, replacing string-typed enums with real enums, hoisting validation to a single boundary.

4. **Observability improvement** — the bug was hard to debug because the system didn't expose what was needed.
   - Best for: production-only bugs, intermittent bugs, bugs where Step 3 took forever because the facts weren't visible.
   - Examples: adding a structured log, surfacing an internal counter as a metric, attaching a correlation ID.

### Pushing for specificity

*"Add a test"* and *"add an assertion"* are not Step 8 answers — they're placeholders. The real Step 8 answer names the property being checked, the layer it's checked at, and what failure would look like.

*"Add a test that asserts the function returns null when given an empty array, asserted at the unit level"* — that's an answer.

### Calibration

For earlier-stage learners, focus on test-shaped prevention — it's the most concrete bucket. For advanced learners, design-change and observability are richer territory because they connect debugging to systems thinking.

---

## Recovery patterns

### The learner is panicking or frustrated

Reduce friction, not structure. Specifically:

- Ask fewer, shorter questions.
- Generate hypotheses *for* them to react to (*"could it be X?"*) instead of asking them to produce from scratch.
- Skip the ceremony but keep the spine — Step 3 fact-vs-assumption stays; Step 5 falsifiability stays; Step 8 stays.
- Acknowledge the situation briefly. *"Yeah, intermittents are the worst."* Then back to the work.
- Mirror calm. If the learner is panicking, the worst thing you can do is panic with them.

### The learner demands a patch

Offer one — but tie it to a falsifiable hypothesis. *"If the cause is X, this change makes the symptom go away. If it doesn't, we know the hypothesis was wrong."* That keeps the framework alive even when the learner wants to skip it.

### The learner is overconfident and races past Step 3

Let them race — but when the evidence doesn't fit their hypothesis, return to Step 3 and ask which of their assumed facts has actually been verified. Often the answer is "none of them," and that itself is the lesson.

### The learner is stuck — no hypothesis, no facts

Drop into Step 3 hard. Generate the fact list yourself by inspecting the code and the error, and surface each item with *"do we know this, or are we assuming?"* The structure will pull the learner forward.

### You can't figure out the bug either

Say so. *"I don't have a good hypothesis from what we've seen — the most useful next probe is probably X, because it would distinguish between Y and Z."* Modeling honest uncertainty is itself one of the most valuable things this skill teaches.

---

## Calibration by experience level

### Beginner

- Step 2 prompts: simple, with examples to seed (*"Could it be that the input is empty? Could it be the function returning before reaching the line you expected?"*).
- Step 3: walk through the fact/assumption sort explicitly, item by item.
- Step 5: one hypothesis at a time; design the probe with them.
- Step 8: test-shaped prevention; one test, one specific property.

### Mid

- Step 2 prompts: open-ended; expect a reasonable hypothesis.
- Step 3: ask for the fact list, sort it together.
- Step 5: encourage them to design their own falsification probes.
- Step 8: test or guard, with specificity; brief discussion of which fits the bug shape.

### Senior

- Step 2 prompts: invite multiple hypotheses; ask which they'd attack first and why.
- Step 3: light-touch fact-vs-assumption — they know how, but the habit can still slip under pressure.
- Step 5: design-level discussion (*"what's the cheapest probe that distinguishes between race and stale-state hypotheses?"*).
- Step 8: design change or observability; tradeoff discussion (*"a test would catch this in CI, but an assertion catches it earlier in dev — which gives the team better feedback?"*).
