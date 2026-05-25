# Question patterns

This document is the catalog of question shapes that work for retrieval practice. Use it when you're constructing a question and want a sharper template, when you're varying question types across an ongoing session, or when you're calibrating a pattern for a particular experience level.

The catalog covers what *to* do. The bottom section covers what *not* to do — the recognition-based shapes that are explicitly forbidden, and the failure modes for each preferred shape.

A few principles before the catalog:

- **Generation over recognition.** Every preferred shape requires the learner to *produce* an answer from memory or reasoning. The forbidden shapes test recognition only.
- **Specificity beats abstraction.** A concrete prompt (*"what happens with empty input?"*) produces sharper diagnosis than an abstract one (*"how does it handle edge cases?"*).
- **One cognitive demand per question.** Multi-part questions split the learner's attention; the answer you get is shallower than you'd have gotten from one focused question.
- **Vary shapes across a session.** Three recall questions in a row is a quiz. One of each preferred shape, mixed thoughtfully, is a session.

---

## The catalog of preferred shapes

### 1. Explain-in-your-own-words (mechanism explanation)

**Cognitive task:** organize and produce a coherent account of how/why something works. Tests integration of knowledge.

**Construction:** *"Explain [target] in your own words, as if [audience]."* The audience framing matters — it gives the learner an organizing principle. *"To a colleague who only knows callbacks"* is concrete; *"to someone"* is vague.

**Good examples:**

- *"Explain async/await in your own words, as if to a colleague who's only used callbacks."*
- *"Walk me through `loginUser` from memory — what it does and why it's structured that way."*
- *"In your own words, what's the difference between a closure and an object holding state?"*

**Bad versions:**

- *"What is async/await?"* — too open, invites recitation of a definition rather than understanding.
- *"Explain async/await."* — no audience, no constraint, no organizing principle.

**Calibration:**

- Beginner: pick a specific concrete behavior to explain (*"explain what await does when the promise resolves"*).
- Intermediate: explain a mechanism with a constraint (*"explain async/await to a callback-only audience"*).
- Advanced: explain a *design choice* (*"explain why this function uses async/await rather than callbacks — what tradeoffs are baked in?"*).

---

### 2. Prediction (what happens if...?)

**Cognitive task:** run the mental model forward and report the output. The deepest form of retrieval — produces vs. describes.

**Construction:** *"What would [target] do with [specific input/scenario]?"* The input must be specific enough that the prediction has a single answer (or a small enumerable set of answers). Vague predictions invite hand-waving.

**Good examples:**

- *"What would `loginUser` do if you pass an empty string as the email?"*
- *"What happens if two requests hit this endpoint at the exact same time?"*
- *"What would you predict if the database call timed out halfway through?"*

**Bad versions:**

- *"How does it handle bad input?"* — too abstract; the learner can answer in generalities without committing.
- *"Does it work with empty input?"* — yes/no question; produces no signal.

**Calibration:**

- Beginner: common edge cases (empty, null, single element).
- Intermediate: less common cases (concurrent calls, dependency failure mid-operation).
- Advanced: pathological cases (huge inputs, partial failures, ordering interleavings).

**Failure modes to push back on:**

- Hand-waving (*"it'd probably throw or something"*) — push for specifics. *"Throw what? Return what? Hang?"*
- Refusal to commit (*"I'd have to look at the code"*) — that's the point. The prediction *is* the test. *"Best guess from memory."*

---

### 3. Transfer (where else does this apply?)

**Cognitive task:** generalize from a specific instance to a class of cases. Tests whether understanding is portable.

**Construction:** *"Where else in [context] would this same [concept/pattern] apply?"* The context can be the same codebase, a different language/framework, a different problem domain, or a counterfactual.

**Good examples:**

- *"Where else in this codebase would dependency injection be the right call?"*
- *"How would this same idempotency idea show up in a checkout flow?"*
- *"If we were writing this in a language without async/await, what would change about the structure?"*

**Bad versions:**

- *"Can you give an example of dependency injection?"* — too generic; allows the learner to recite a textbook example without thinking.
- *"Is this an example of dependency injection?"* — recognition, not transfer.

**Calibration:**

- Beginner: transfer within the immediate context (*"where else in this file?"*).
- Intermediate: transfer within the codebase (*"where else in this repo?"*).
- Advanced: cross-paradigm transfer (*"how would this look in a streaming context?"*) or critique-of-misapplication (*"where would this be the wrong choice?"*).

---

### 4. Compare / contrast

**Cognitive task:** articulate the distinguishing features of two related things. Tests whether the learner has a *differentiated* model or just a fuzzy cluster.

**Construction:** *"What's the difference between [X] and [Y]?"* — where X and Y are similar enough to be conflatable. *"What's the difference between a closure and a function"* is bad (too different). *"What's the difference between a closure and an object holding state"* is good (genuinely conflatable).

**Good examples:**

- *"What's the difference between a closure and a class instance with a field?"*
- *"How does `async/await` differ from `Promise.then()` — what does each let you express cleanly?"*
- *"What's the difference between optimistic locking and pessimistic locking?"*

**Bad versions:**

- *"Is closure different from object?"* — yes/no question.
- *"What's the difference between async/await and pizza?"* — not conflatable; no cognitive work.

**Calibration:**

- Beginner: surface-level comparison (*"what's the syntactic difference?"*).
- Intermediate: behavioral comparison (*"when does each one give you different results?"*).
- Advanced: design-level comparison (*"when would you reach for one over the other, and why?"*).

---

### 5. Critique (what's wrong / what would break?)

**Cognitive task:** find the flaw or fragility in a design or implementation. Tests whether the learner has a *predictive* model strong enough to anticipate failure.

**Construction:** *"What would break [target] if [something changed/were missing]?"* or *"What's a weakness of this design?"* The framing should invite genuine critique, not defensive rationalization.

**Good examples:**

- *"What would break `loginUser` if the database started returning users in random order?"*
- *"What's the most fragile part of this caching design?"*
- *"If we removed the validation block, what's the first bug that would appear?"*

**Bad versions:**

- *"Is this code good?"* — recognition; gives no signal.
- *"What are the bugs in this code?"* — implies bugs exist; pre-loads the answer.

**Calibration:**

- Beginner: identify a specific missing safety (*"what's the validation protecting against?"*).
- Intermediate: identify a class of failure (*"what kind of input would break this?"*).
- Advanced: identify a structural fragility (*"where would this design start to crack under load?"*).

---

### 6. Edge case reasoning

**Cognitive task:** identify boundaries the learner thinks the code or concept might mishandle. Tests both knowledge and *imagination* — what edge cases come to mind unprompted?

**Construction:** *"What edge cases does [target] need to handle? Which of them does it actually handle well?"* The two-part structure invites both generation (list them) and critique (evaluate them).

**Good examples:**

- *"What inputs would you want to test `loginUser` against, beyond the happy path?"*
- *"Where does this function's contract get fuzzy — what inputs are 'allowed but weird'?"*
- *"What's an edge case the original author probably didn't think about?"*

**Bad versions:**

- *"Does it handle edge cases?"* — yes/no.
- *"What's an edge case?"* — definition trivia.

**Calibration:**

- Beginner: enumerate concrete inputs (empty, null, very long, very short).
- Intermediate: identify edge cases in *behavior* (race conditions, partial failures, repeated calls).
- Advanced: identify edge cases in the *design* (what assumptions does this code make about its callers? where might those assumptions fail in a new context?).

---

### 7. Debugging reasoning (what symptom would this produce?)

**Cognitive task:** reason forward from a hypothetical bug to its observable effect, or backward from a symptom to its likely cause. Tests whether the learner can connect mechanism to observation.

**Construction:** *"If [hypothetical bug], what would the user / test / log see?"* or *"If a user is seeing [symptom], what are the top candidates?"*

**Good examples:**

- *"If we forgot the `await` on line 12, what would the user actually experience?"*
- *"A user reports that their login 'sometimes fails for no reason' — what would your top three hypotheses be?"*
- *"If this function silently swallowed errors instead of throwing, what's the first thing that would go wrong?"*

**Bad versions:**

- *"Is there a bug here?"* — recognition.
- *"Find the bug."* — that's smart-debug's territory; this is about *reasoning*, not bug hunting.

**Calibration:**

- Beginner: forward reasoning (*"what would happen if X?"*).
- Intermediate: backward reasoning (*"what would cause symptom Y?"*).
- Advanced: triangulation (*"two symptoms — A and B — appear together. What's the single cause that would explain both?"*).

---

### 8. Design tradeoff reasoning

**Cognitive task:** articulate *why* one design choice was made over another, including what was given up. Tests whether the learner sees design as choice rather than necessity.

**Construction:** *"Why might the author have chosen [X] over [Y]?"* or *"What does this design give up in exchange for what it provides?"*

**Good examples:**

- *"Why is this function using a queue rather than calling the dependency directly? What does the queue buy us, and what does it cost?"*
- *"What's the tradeoff in caching here? When would the cache be the wrong call?"*
- *"Why do you think the author chose async/await here rather than callbacks?"*

**Bad versions:**

- *"Is this the right design?"* — leading.
- *"What does the queue do?"* — mechanism, not tradeoff.

**Calibration:**

- Beginner: identify the obvious tradeoff (*"why might we want this to be async?"*).
- Intermediate: name multiple tradeoffs (*"speed, complexity, failure modes — how does this design weigh them?"*).
- Advanced: critique the tradeoff (*"is this the right tradeoff for this codebase's actual constraints?"*).

---

### 9. Connection / integration

**Cognitive task:** connect the target to other things the learner already knows. Tests integration into a broader model.

**Construction:** *"How does [target] relate to [related thing]?"* The related thing should be something the learner has plausibly encountered.

**Good examples:**

- *"How does idempotency relate to retry safety?"*
- *"How does this caching layer interact with the database's own caching?"*
- *"How is this similar to or different from the pattern used in [other module]?"*

**Bad versions:**

- *"How are these related?"* — too vague.
- *"Are they related?"* — yes/no.

**Calibration:**

- Beginner: connect to immediate context (*"how does this function relate to the one above it?"*).
- Intermediate: connect across modules.
- Advanced: connect across paradigms or concerns (*"how does this caching idea relate to the rate-limiting idea?"*).

---

## What not to use

These shapes are forbidden because they test recognition, not generation:

### Multiple choice

*"Is X (a) foo, (b) bar, or (c) baz?"* The learner can succeed by recognizing the right answer without producing the knowledge themselves. Pattern-matching beats memory. **Never use.**

### True / false

*"True or false: closures capture by reference."* 50% baseline; produces no signal about understanding. **Never use.**

### Fill-in-the-blank

*"`async` functions return a ___."* Trivial recall; tests vocabulary, not understanding. **Never use.**

### Definition trivia

*"What is dependency injection?"* The learner can recite a textbook definition without being able to apply or recognize the concept. **Never use.**

### Recognition prompts

*"Is this an example of dependency injection?"* Tests whether they can match a label to a pattern, not whether they understand the pattern. **Never use.**

### Yes / no questions

*"Does this function handle empty input?"* No signal in the answer. **Never use** as a primary question. (You may use them as a quick *clarifying* question — *"do you want to continue?"* — those aren't test questions.)

### Single-word recall

*"What's the return type of `loginUser`?"* Lookup, not understanding. **Avoid** unless there's a specific pedagogical reason (e.g., the type itself encodes a design decision worth discussing).

---

## Cross-shape principles

### Anchor every question

A question should anchor to something specific — a function name, a concrete input, a named alternative. Anchored questions have a single defensible diagnostic; unanchored questions invite hand-waving.

- **Anchored:** *"What would `loginUser` do if you pass null for password?"*
- **Unanchored:** *"How does the function handle bad input?"*

### Demand specificity in answers

When the learner answers with generalities (*"probably an error"*, *"it depends"*, *"some kind of edge case"*), push for the specific. The hand-wave is the diagnostic — they don't actually have the model loaded. Pushing politely (*"throw what specifically? Return what?"*) is the work that produces retrieval.

### Vary question shape across a session

In an ongoing session, alternate between recall, prediction, transfer, and one of the others. Three recall questions in a row is a quiz; one mechanism explanation followed by a prediction followed by a transfer is a *session*.

A reasonable mid-session sequence:

1. Mechanism explanation (Step 3 territory).
2. Prediction on a specific input (Step 4).
3. Critique or edge case (extension of Step 4).
4. Transfer to another context (Step 7).
5. Reflection (Step 8).

Five challenges, five different cognitive demands.

### When the learner answers a question better than you asked it

Sometimes the learner produces something deeper than the question deserved — they take a recall prompt and turn it into a design critique unprompted. That's not a problem to redirect; it's a signal to follow them up the ladder. *"That's a richer answer than the question asked for — let me follow that thread."*

That kind of session is the best kind. It means retrieval is happening *and* the learner is doing the work of integration on their own. The framework was scaffolding; the learner just outgrew it for a moment. That's success.
