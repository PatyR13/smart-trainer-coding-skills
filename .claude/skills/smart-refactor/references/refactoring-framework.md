# The refactoring framework

This document expands on the 7-step operational flow in `SKILL.md` and the design-reasoning posture this skill is built around. Use it when you need depth on a step, when you're tempted to short-circuit the framework, or when you want to understand *why* refactoring as taught here looks different from refactoring as practiced in most AI tools.

The framework is engineering design reasoning written down. It's the difference between *"this is cleaner"* (which is an aesthetic claim) and *"this trades one cost for another — here's the math"* (which is engineering).

---

## The two failure modes this skill exists to prevent

### Cargo-cult refactoring

The learner has read about a pattern — Strategy, Repository, Adapter, Hexagonal Architecture, "Clean Code" — and now sees it everywhere. They want to apply it because they've seen the name applied, not because the problem the pattern solves is present.

Signatures:

- *"This should be a Strategy pattern."* (Why? What problem does it solve here that's worth the indirection cost?)
- *"This is too long, it needs to be split up."* (Why is length itself a problem? What gets *easier* if you split it?)
- *"We should extract this duplication."* (Is it duplication, or is it two things that happen to look alike right now and will diverge later?)
- *"This isn't very SOLID."* (Which letter? Why does it matter here? What would the SOLID version cost?)

The cure: **always ask *"what problem does this solve here, and what does it cost?"*** before any pattern is adopted. Patterns are tools; tools without problems are decoration.

### Elegance theater

The learner (or the AI) makes a refactor that *looks* better — shorter, smoother, named — without actually being better. Cosmetic improvement is mistaken for design improvement.

Signatures:

- Replacing an explicit loop with a clever one-liner that takes longer to read.
- Renaming a variable for personal taste rather than codebase consistency.
- Introducing an abstraction with one user ("just in case we need flexibility later").
- "Cleanup" passes that change a lot of code without making anything actually easier.

The cure: **Step 6 (compare versions) honestly.** What *specifically* improved? What got more complex? If you can't name what improved more concretely than *"it's nicer"*, the refactor isn't earning its cost.

---

## The intent-reading habit

The single most underused move in refactoring is reading the *intent* behind the existing code. Most "ugly" code is the result of constraints the new reader doesn't see.

Eight common intent reasons code looks the way it does:

1. **Readability** — clarity at the cost of concision. A "verbose" function may be deliberately explicit.
2. **Performance** — repeated work avoided, allocations minimized, hot paths inlined. Refactor at your peril.
3. **Backward compatibility** — keeping a contract that callers depend on. A "weird" signature may be load-bearing for someone else.
4. **Delivery pressure** — shipped under deadline. Cleanup was deferred *deliberately*; doing it now might still be the wrong call.
5. **Explicitness** — preferring obvious-but-verbose to clever-but-subtle. Often the right call in teams with rotating ownership.
6. **Debuggability** — flat structure that's easy to stack-trace through. Splitting into 12 helpers may make production debugging harder.
7. **Defense against a real prior bug** — guards exist because something specific went wrong before. Removing them re-opens the bug.
8. **Language or framework limitation** — code is shaped around a quirk of the toolchain. Removing the shape may break things in ways that don't surface until later.

When you spot a structure that surprises you, *try* to read it as the answer to one of these constraints before treating it as a mistake. If the learner doesn't know the history, that ambiguity itself is data — it raises the cost of refactoring (you're modifying code whose load-bearing constraints you can't fully see).

---

## The tradeoff-axis vocabulary

Every refactor moves the code along several axes simultaneously. The teachable skill is naming the axes — and noticing that *every* refactor trades some against others. There are no free refactors.

The axes worth naming:

- **Readability** — how easy is this to skim and get the gist?
- **Local clarity** — how easy is it to understand *this function* in isolation?
- **Distant clarity** — how easy is it to understand the system as a whole?
- **Indirection** — how many jumps does a reader make to follow the logic end-to-end?
- **Complexity (cyclomatic)** — branches, paths, decisions per unit.
- **Coupling** — how much do parts of the code know about each other?
- **Cohesion** — how related are the responsibilities inside a unit?
- **Flexibility** — how easy is it to extend behavior in anticipated ways?
- **Testability** — how easy is it to write meaningful tests?
- **Performance** — runtime, allocations, latency, throughput.
- **Cognitive load** — how much does a reader have to hold in their head at once?
- **Diff size / blast radius** — how many places are affected by this refactor?
- **Learning curve** — how much new vocabulary or pattern does a new reader need to learn?
- **Debuggability** — how easy is it to step through under failure?

A useful exercise mid-session: pick two axes the proposed refactor moves in *opposite* directions on. Name the trade explicitly. *"This improves local clarity and testability; it costs indirection and slight overhead on the hot path."* That sentence is engineering. *"This is cleaner"* is not.

---

## Step-by-step depth

### Step 1 — Intent discovery

The most distinctive step in the framework, and the one most often skipped.

**Why it matters:** code without context is a question, not a verdict. Refactoring code whose intent you don't understand has a non-trivial probability of breaking something you couldn't see.

**How to do it:** ask one of the prompts in `SKILL.md` — *"what is this code trying to optimize for?"* etc. Then *try* to articulate at least one plausible intent yourself, even if it turns out to be wrong. The hypothesis disciplines the next steps.

**If the learner doesn't know the history:** that's information. Refactor anyway if the stakes are low, but say so. *"We don't know why this is shaped this way — let's keep the change tight to reduce risk."* That sentence is the lesson.

### Step 2 — Smell identification

The temptation here is to dump everything you see. Resist it.

**Pick three or four candidate smells, ranked.** Ranking is itself part of the teaching — *"the most significant thing I'd point at is X; less importantly there's also Y and Z."* The learner needs to know that not all smells are equal.

**Ask the learner first.** Often they're seeing a smell you're missing (they know the history of edits to this file; you don't). Often they're missing a smell that's obvious to you (because they're inside the code). The gap between their list and yours is the teaching moment.

**Use the catalog as a checklist, not a script.** `references/code-smells.md` lists common shapes with recognition cues. Use it to *recognize*, not to enumerate.

### Step 3 — Tradeoff analysis (required)

The keystone of the skill. Without explicit tradeoff naming, the rest is decoration.

**Format that works:** for each candidate refactor, write one line of the form *"improves [axis A]; costs [axis B]."* Force yourself to name a cost. If you cannot name a cost, the refactor is probably either too small to matter or you're missing something.

**The learner does this work, not just you.** Ask: *"if we extracted that into a helper, what gets better and what gets worse?"* The articulation is the lesson. Filling in the tradeoff for them is the failure mode.

**The "no costs" red flag.** If a refactor seems to have only upsides, double-check. Either it's a free improvement (rare but real — usually a localized rename or a typo fix), or you're not looking at the cost honestly. Common hidden costs: more files to open, more vocabulary for new readers, more indirection in debug traces, more abstraction to mentally invert, more places to update when behavior changes.

### Step 4 — Alternative design exploration

Generate options collaboratively. The point is not to enumerate every possible move — it's to make clear that *there are multiple options* and that picking among them is design work.

**The categories of move from `SKILL.md` (extraction, composition, boundary movement, simplification, immutability, dependency inversion, contract clarification, responsibility separation) are starting points.** Pick two or three that fit the smells from Step 2, and discuss them as alternatives.

**Alternative-by-counterfactual works well here.** *"If we extracted, we'd get X. If instead we left the function whole and just renamed the chunks, we'd get Y. If we pushed this responsibility up to the caller, we'd get Z. Which fits the actual problem best?"*

**Be willing to land on "don't refactor."** Sometimes the conclusion is that none of the alternatives are worth the cost. That's a legitimate finding — and a much better one than "the rewrite was cleaner but introduced two new bugs."

### Step 5 — Guided refactoring (the escalation ladder)

Only run this step once Steps 1–4 are done. The order matters: thinking before doing.

**The four rungs:**

1. **Hints.** *"How would you start? Where's the natural boundary?"* The learner produces the move.
2. **Structural guidance.** *"Pull lines 14–22 into a function. What's the signature?"* You frame; they fill.
3. **Partial rewrite.** You produce the trickiest part — usually the part that demonstrates the move — and they complete it.
4. **Full rewrite.** Only when justified.

**When to use each rung:** match it to the learner's level and the size of the move. A beginner doing their first extraction may need rung 2 from the start; an advanced learner doing a small simplification can probably handle rung 1.

**When the learner asks for a rewrite directly:** offer it, but bracket it with Steps 1, 3, 6, and 7. Preserving the pedagogical spine even when the learner skips the middle is the skill's most important compromise.

**A note on diff size:** smaller refactors are almost always more teachable than large ones. A focused move (*"extract this helper"*) produces a clean comparison in Step 6. A large move (*"restructure the module"*) often produces a comparison where so many things changed at once that the tradeoffs blur. When you can, prefer the small move.

### Step 6 — Compare versions (required)

The antidote to cargo-cult.

**The three questions:**

- *What actually improved?* (Be specific. "Cleaner" isn't an answer. *"Easier to test in isolation"* is.)
- *What got more complex?* (Force this. If the answer is "nothing", you're not looking hard enough.)
- *What tradeoff did we accept?* (Name it in tradeoff-axis vocabulary.)

**If the comparison shows a net negative:** *revert*. This is a legitimate outcome. The lesson is in noticing that the move wasn't worth it — that lesson is more valuable than the diff.

**If the comparison shows a wash:** consider reverting. Code churn without clear improvement has a real cost (review time, merge conflicts, git-blame noise). *"This is roughly the same shape, just rearranged — let's leave it"* is fine.

### Step 7 — Transfer (required)

The recognition is what the learner takes with them. Anchor it:

- *"Where else in this codebase do you suspect the same smell?"*
- *"How would you recognize this earlier — before it gets worse?"*
- *"What's an example where this smell is actually the right design choice?"*

The last question is the most important and the most often skipped. A learner who only sees "smell → refactor" is the cargo-cult learner. A learner who can also see "smell → leave it, here's why" is doing engineering.

---

## Recovery patterns

### The learner asks for an immediate rewrite

Provide one — but bracket it. Steps 1, 3, 6, and 7 still run. Compressed is fine; skipped is not. *"Here's a rewrite. Before we look at it, what was this code trying to optimize for? Now that we've looked at it, what did the rewrite cost?"*

### The learner is over-attached to a named pattern

When they want to apply Strategy / Repository / Hexagonal / whatever, your move is: *"what specific problem does that pattern solve here? What does it cost?"* If they can answer cleanly, follow them. If they can't, the pattern isn't the right tool — and that's the lesson.

Don't argue *against* the pattern. Ask the question, let the answer (or its absence) be the data.

### The learner is intimidated by the code

Some learners freeze in front of code that looks "advanced" or unfamiliar. The cure is structural decomposition: *"let's break the question down. What does this function take in, and what does it return?"* Get them oriented at the boundaries before discussing internals. By the time they understand the boundaries, the inside is usually less intimidating.

### You're tempted to refactor more than the learner needs

The perfectionism failure mode. Once the learner is satisfied with the move, *stop*. The point is the learner's reasoning, not the code's aesthetic optimum.

### The session shows the code is fine as-is

This is a *success outcome*, not a failure. The learner has done the analysis and concluded that the cost of refactoring exceeds the benefit. Affirm the reasoning, run Step 7 (where else does this come up?), and end the session.

A session that ends with *"this is fine, here's why"* is one of the most pedagogically valuable shapes this skill produces. It directly counteracts cargo-cult tendencies.

---

## Calibration by experience level

### Beginner

- Step 1 prompts: concrete and gentle (*"what do you think this is trying to do?"*).
- Step 2 smells: concrete and visible (long function, unclear naming, duplication that's actually duplication).
- Step 3 tradeoffs: one axis at a time, with you naming the cost on the first pass; them on the second.
- Step 4 alternatives: usually one alternative, sometimes two.
- Step 5 escalation: rung 2 or 3 early.
- Step 7 transfer: stays inside the current file.

### Intermediate

- Step 1: invite hypothesis-generation about intent.
- Step 2: three to four candidate smells, ranked by you, with prioritization discussed.
- Step 3: learner names the tradeoff axes; you check them.
- Step 4: two or three alternatives, with comparison.
- Step 5: rung 1 to 2.
- Step 7: transfer across the codebase or across patterns.

### Advanced

- Step 1: invite architectural reading — what design choices does this code reveal about the system?
- Step 2: structural smells (layering violations, hidden coupling, leaky abstractions); pattern critique invited.
- Step 3: second-order tradeoffs — team learning curve, future flexibility, blast radius of failures.
- Step 4: multiple architectural alternatives; *"don't refactor"* is on the table from the start.
- Step 5: rung 1; let them propose moves and critique them yourself.
- Step 7: architectural transfer — *"where else is this assumption baked in? where is the same pattern legitimately required?"*
