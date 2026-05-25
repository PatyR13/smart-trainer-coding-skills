# The retrieval framework

This document expands on the 8-step operational flow in `SKILL.md` and explains the cognitive-science basis that makes retrieval practice work. Use it when you need depth on a step, when you're unsure how to respond to a particular learner state, or when you want to understand *why* the framework is shaped the way it is.

The framework is structured active recall written down. It's the difference between *"do you understand this?"* (which produces fluent feel-good answers and almost no learning) and *"produce, from memory, the thing you claim to understand"* (which produces friction and a lot of learning).

---

## Why retrieval practice works

Three findings from the cognitive science of learning are load-bearing here:

1. **The testing effect.** Forcing the brain to *generate* an answer (vs. *recognize* one) strengthens the memory trace dramatically more than passive review. Retrieval *is* learning — not just measurement of learning. This is why every question in this skill must require generation, never recognition.

2. **Desirable difficulties.** Learning is most durable when it requires effort. Easy review produces a feeling of fluency that doesn't predict retention. Productive struggle does. This is why Step 6 scaffolds rather than reveals — the *trying again* is where the durable encoding happens.

3. **Calibration is a distinct skill.** Knowing what you know is itself a teachable skill, and most engineers are poor at it. This is why Steps 2 and 8 bracket every session — the gap between pre-test and post-test confidence is the diagnostic.

You do not need to lecture the learner about cognitive science. You just need to *act* in accordance with it. The constraints in `SKILL.md` are the science applied; this document is the science explained.

---

## The illusion of competence

Familiarity is not understanding. A learner who has read a function five times feels like they understand it — the code is *familiar*. Asking them to *produce* the function from memory, or to *predict* what it does with an unusual input, often reveals that the familiarity was masking gaps.

The illusion has predictable signatures:

- *"Yeah, I get this"* — without an example or a prediction to back it up.
- Smooth, confident explanations that use the right vocabulary but skim the mechanism.
- Confidence that doesn't budge in the face of a failed prediction.
- The learner uses the question's own words back at you instead of producing their own framing.

When you see these, lean harder on prediction (Step 4) and transfer (Step 7). The illusion of competence cannot survive a real prediction — either the prediction lands, in which case the understanding was real, or it falls apart, in which case the illusion has been safely punctured.

---

## The misconception / incomplete-knowledge / uncertainty trichotomy

When the learner gives a wrong or incomplete answer, your response depends on *which kind* of wrong it is. This is the single most consequential diagnostic call in the framework.

### Misconception

The learner has a confident, internally coherent model — and it's wrong. Example: *"Closures capture by value, so the function always sees what was there originally."* This is wrong, but it's also a *theory* — the learner has constructed something and believes it.

**Response:** Don't say "wrong." Surface the contradiction with a question that puts the misconception to a test it will fail. *"What would you predict if I changed the variable after the closure was created?"* The misconception has to be falsified, not contradicted. Telling the learner they're wrong rarely changes the model — making the model produce a wrong prediction does.

### Incomplete knowledge

The learner has part of the model but is missing a piece. Example: *"I know `await` waits for the promise, but I'm not sure what the function is doing while it waits."* This isn't wrong — it's partial. The learner is signaling exactly what's missing.

**Response:** Give the missing piece concisely, then resume testing. Don't Socratic-question someone into discovering a fact you could have stated in one sentence. The Socratic mode is for *misconceptions*, not for *gaps*. Treating gaps as misconceptions is condescending and slow.

### Uncertainty

The learner is hedging because they genuinely don't know. *"I think... maybe... it's something like..."* They're not wrong; they're flagging that they're guessing.

**Response:** Acknowledge the hedge. Decide whether to fill the gap (if it's a fact) or scaffold (if it's reasoning). Either way, name what you're doing — *"that's a fact you'd just have to know,"* or *"let's think it through — what's the first thing this code touches?"*

### Why the distinction matters

Treating all three the same way is the most common failure mode of Socratic teaching tools. They Socratic-question someone who lacks a fact (condescending), or they hand a fact to someone with a confident misconception (the misconception survives), or they're impatient with hedging (the learner shuts down).

When in doubt: a confident wrong answer is almost always a misconception. A hedged or partial answer is almost always incomplete knowledge or uncertainty.

---

## Step-by-step depth

### Step 1 — Scope the test target

The category of target steers the question types:

- **Code symbol or file** — questions can be concrete: predict the output, walk through the control flow, identify edge cases.
- **Architectural area** — questions are about relationships: how do these modules communicate, what's the contract, where would a change ripple.
- **Concept** — questions are about application: mechanism, where it fits, where it breaks down, comparison to neighbors.
- **Debugging concept** — questions are about recognition and reasoning: what symptoms would this produce, how would you find it, what's the fix archetype.

If the invocation is ambiguous, ask **one** short question to pin it down. Don't ask for taxonomic precision — *"function or module?"* is enough, *"is this a behavioral or structural test target?"* is too much.

### Step 2 — Confidence calibration (pre-test)

This step has two functions:

1. **Anchor for Step 8.** Without the pre-test rating, the post-test rating has nothing to compare against.
2. **Surface the illusion of competence to the learner themselves.** Saying *"I'm confident"* out loud creates a small commitment that makes the eventual diagnostic more salient.

If the learner refuses to rate or brushes it off, *don't push*. The act of being asked is already half the value. Note their resistance silently and move on.

For ongoing sessions, you don't need to re-anchor for every question — the session-level anchor is enough. But if difficulty has stepped up significantly, a quick mid-session recalibration is fine.

### Step 3 — Active recall challenge

The opening question should be **broad enough to require organization, narrow enough to be answerable.** *"Tell me about authentication"* is too broad (the learner doesn't know which 5% to produce). *"What's the return type of `loginUser`?"* is too narrow (and is trivia).

Good middle: *"Walk me through what `loginUser` does, from memory, as if you were explaining it to a colleague who hasn't seen the file."* The learner has to organize what they know, decide what's relevant, and produce a coherent sequence — all of which are real cognitive work.

Wait for the answer. Don't pre-empt with a definition. The learner's first attempt — including its gaps — is the diagnostic.

### Step 4 — Prediction challenge

Prediction is the hardest test of retrieval, because the learner has to *run their mental model* — not just describe it. Their mental model and the actual code/concept either agree or disagree, and the answer reveals which.

Good prediction prompts:

- Specify the input or scenario concretely. *"What would happen if the email field is an empty string?"* (concrete) beats *"how does it handle bad input?"* (too abstract).
- Ask for the prediction *before* showing the relevant code section. Once they've seen it, it's no longer a prediction.
- Push back on hand-waving. *"I think it'd probably throw or something"* is not a prediction — *"would it throw, return null, or return an error object?"* turns it into one.

If the learner predicts correctly, briefly acknowledge and follow up: *"Right — and why does it do that, structurally?"* Right predictions still benefit from articulation.

If the learner predicts incorrectly, go to Step 5 — but specifically the part of Step 5 that asks *what's the underlying model that produced this prediction?* The prediction is a symptom; the model is the bug.

### Step 5 — Misconception diagnosis

See the trichotomy section above. The diagnostic workflow:

1. Notice the answer is wrong or incomplete.
2. Decide: misconception (confident, coherent) / incomplete (partial) / uncertainty (hedged)?
3. Respond accordingly — falsifying question, missing fact, or acknowledge-and-scaffold.

**Sub-pattern — the productive surprise.** Sometimes the learner answers in a way that's not technically wrong but reveals an interesting alternative framing or a place where their model differs from yours. That's not a misconception — it's an invitation. *"That's a different way to think about it than I'd have started — say more about what's leading you there."* You may learn something. The skill works both ways.

### Step 6 — Progressive scaffolding (the four-rung ladder)

When the learner is stuck, climb slowly:

- **Rung 1 — small hint.** A single nudge, usually pointing at *where* to look rather than *what's there*. *"Look at what happens after the await."*
- **Rung 2 — stronger hint.** A more directive nudge that names the relevant element. *"Think about the variable's value at the moment the closure is invoked."*
- **Rung 3 — partial framing.** Give the structure without the punch line. *"Closures hold a reference, not a snapshot."*
- **Rung 4 — full explanation.** State the answer. Then **immediately re-ask the original question, rephrased**, so the learner gets to do the retrieval that was missing.

Most learners need rungs 1 and 2 only. The temptation to skip to rung 4 is the failure mode — it produces a learner who's heard the answer but hasn't retrieved it.

**Rung 4 is not a failure of the framework.** Sometimes the answer is just a fact the learner doesn't have. State it, then loop back to retrieval. *"OK — closures hold references. Now: what would you predict happens when I modify the variable?"* That post-explanation prediction is where the encoding lands.

### Step 7 — Transfer challenge

Transfer is what separates *"I retrieved this answer once"* from *"I can apply this concept elsewhere."* The cognitive science is unambiguous: knowledge that doesn't transfer almost certainly won't survive a month.

Five transfer-question shapes that work:

- **Counterfactual** — *"how would this change if X?"*
- **Concurrency** — *"what happens with two of these in flight?"*
- **Partial failure** — *"what if the dependency returns half-broken data?"*
- **Testability** — *"how would you test this, and what's the hardest case?"*
- **Cross-context** — *"where would this same idea show up elsewhere in the codebase or in a different language?"*

If transfer fails (the learner can't apply the concept in the new context), don't treat it as a deficiency — treat it as the *diagnostic moment*. The previous steps may have been producing a recitation of what they saw, not understanding. Return to Step 5 with a sharper falsifying question.

### Step 8 — Reflection / recalibration (post-test)

This step closes the loop. The structure:

1. **Recall the Step 2 anchor.** *"You said 'high confidence' at the start."*
2. **Ask the recalibration question.** *"Where are you now?"*
3. **Name what shifted.** If the rating dropped, frame it as a successful outcome: *"That's a successful session — you found the gaps you couldn't see at the start."* If it stayed high, name what was tested: *"You held up — predictions were solid, transfer landed."*
4. **Leave one specific thing to come back to.** *"The interaction between X and Y was the friction point — worth another round on this later."*

If the learner's rating *increased* significantly, be a little skeptical — sometimes the session produces clarity, but sometimes it produces the *illusion of clarity* (they liked the experience and updated emotionally rather than evidentially). The transfer answer from Step 7 is your evidence.

---

## Session-mode guidance

### One-shot

Compress to: Steps 1, 2, 3 (or 4), optionally 5/6, 7, 8. Should fit in 1–3 exchanges. Step 2 and Step 8 stay — they bracket the session even when the session is small.

### Short session (3–5 challenges)

Vary question shape across challenges. A short session typically goes: recall → prediction → transfer or critique. Step 8 reflection covers the whole session, not the last question only.

### Ongoing

Watch for these signals:

- **Productive struggle:** the learner is wrong sometimes, asks for hints occasionally, gets there eventually. This is the target state. Keep going at this difficulty.
- **Too easy:** the learner nails every answer, including transfers. Step difficulty up — move to architecture, edge cases, design critique.
- **Too hard:** the learner can't even attempt several questions in a row. Step difficulty down — return to more concrete prompts, shorter scaffolding ladders.
- **Fatigue:** shorter answers, hedging, *"sure"* responses, declining engagement. Do one more challenge, then offer to stop.

When ending an ongoing session, do a *session-level* Step 8 — name two or three things the learner did well, and one specific gap or shaky area worth revisiting later.

---

## Recovery patterns

### The learner is bored

Increase challenge. Likely the difficulty was too low. Move to transfer or design critique. If they're still bored, end the session — *"you've got this pretty solid, want to come back to it later or pick something harder?"*

### The learner is frustrated or floundering

Decrease challenge. Climb back down the scaffolding ladder. Avoid more transfer questions until they've stabilized on a successful recall.

If frustration is emotional rather than cognitive (e.g., they're tired, or this is a sore spot), name it briefly and stop. *"This one feels heavier — want to come back to it?"* A bad session under fatigue can damage motivation more than a good session helps.

### The learner is gaming the framework

Some learners — especially advanced ones — will start to *anticipate* the structure. They predict before you ask them to predict; they bring up transfer before Step 7. That's fine, even great. The framework isn't ritual — it's a guarantee about coverage. If the learner is already producing what the framework would have asked for, just confirm it's there and move forward.

### The learner says "just tell me the answer"

You can — but make it followed by a retrieval. *"OK: closures hold references. Now, what does that predict about the case where the variable is reassigned after capture?"* The state-then-retrieve pattern preserves the learning even when the learner doesn't want to fight for it.

---

## Calibration by experience level

### Beginner

- Step 2: low/medium/high; don't push for precision.
- Step 3 prompts: concrete, anchored to specific code or examples.
- Step 4 predictions: simple shape (empty input, common edge case).
- Step 5: lean toward incomplete-knowledge interpretation; provide facts more readily.
- Step 6: climb slowly; rungs 1 and 2 should be the workhorses.
- Step 7: transfer stays close to the immediate context — *"how would you test this?"* rather than architectural transfer.
- Step 8: gentle reflection; name one specific thing well done.

### Intermediate

- Step 2: 1–5 scale is fine; comfortable with self-rating.
- Step 3: more open-ended; expect organized retrieval.
- Step 4: predictions about non-obvious inputs, ordering, dependencies.
- Step 5: misconceptions are likely; lean into surfacing them.
- Step 6: rung 1, then jump to rung 3 if needed; intermediates can use partial framings.
- Step 7: counterfactual and cross-context transfer.
- Step 8: name shifts in calibration directly.

### Advanced

- Step 2: optional, but invite an honest read. Advanced learners are often *worse* at calibration in their core areas (overconfidence) and *better* in adjacent areas (knowing what they don't know).
- Step 3: design-level retrieval — *"explain the contract, not the implementation."*
- Step 4: predictions about edge cases, concurrency, failure modes.
- Step 5: misconceptions in advanced learners are often subtle and load-bearing — worth pursuing carefully.
- Step 6: minimal scaffolding; let them struggle longer.
- Step 7: architectural transfer, alternative-paradigm transfer, critique-of-existing-design transfer.
- Step 8: ambitious recalibration; advanced learners benefit from the meta-skill more than they realize.
