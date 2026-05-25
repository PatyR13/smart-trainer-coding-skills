# Evaluation Results

> **Second pass** — re-evaluation after the targeted polish addressing the highest-priority findings from the first pass. Changes since first pass: warnings added to `smart-debug/references/failure-patterns.md`, contradiction reconciled in `smart-test-me/SKILL.md`, trivial-code session shape added to `smart-explain/SKILL.md`, four eval scenarios added to `smart-init`.

## Method

This is a **manual instruction-level evaluation**, not an automated test run.

- 20 markdown eval scenarios were reviewed: 4 per skill across `smart-init`, `smart-explain`, `smart-debug`, `smart-test-me`, `smart-refactor`.
- For each scenario, the corresponding `SKILL.md` (and relevant `references/`) was read. The question evaluated was: *"Are the skill's instructions likely to produce the behavior the eval describes as 'expected,' and unlikely to produce the behavior it describes as 'failure'?"*
- No automated harness was run. No real Claude Code session was executed. Conclusions are about the contract — the SKILL.md instructions and references — not empirical behavior.
- Verdicts measure **instruction alignment**, not **behavioral correctness**. A `PASS` here means the instructions clearly mandate the expected behavior; it does NOT mean the model has been observed doing so under real conditions.
- The verdict upgrades in this pass reflect instruction changes only. The behavioral risks (model judgement under pressure, pattern-matching under low evidence) are reduced but not eliminated by clearer instructions, and remain best addressed by real-session field testing.

---

## Summary

| Metric | Value |
| --- | --- |
| Total scenarios | 20 |
| PASS | 15 |
| PASS WITH RISK | 5 |
| FAIL | 0 |
| Highest-risk skill | Tied — every skill has exactly one PASS WITH RISK; see below |
| Strongest skill (by enforceability) | `smart-test-me` — the no-MCQ rule is the single most categorical contract in the suite, and the post-polish reconciliation closes the only internal contradiction |

**Highest-risk note:** there is no clear single-skill leader anymore. After the polish, each downstream skill has exactly one PASS WITH RISK scenario, and all five remaining risks are similar in shape (judgement-dependent calls under specific pressure). The recurring residual risk across three skills is the *compression-vs-collapse* boundary when users demand brevity — see cross-skill findings.

### Verdict changes since first pass

| Scenario | Before | After | Reason |
| --- | --- | --- | --- |
| `smart-explain/04-trivially-simple-code` | PASS WITH RISK | PASS | Full trivial-code session shape now in SKILL.md, not just Step 2. |
| `smart-debug/04-broad-target-no-clear-cause` | PASS WITH RISK | PASS | Catalog-temptation warnings added at top of `failure-patterns.md` and near the cheat sheet. Residual behavioral risk noted. |
| `smart-test-me` (cross-skill) | contradiction present | reconciled | `What you must not do` first bullet rewritten to align with the hard pedagogical constraint. |

### New verdicts (smart-init, added this pass)

| Scenario | Verdict |
| --- | --- |
| `01-clean-first-onboarding` | PASS |
| `02-learner-volunteers-most-context` | PASS |
| `03-learner-pushes-back-on-process` | PASS WITH RISK |
| `04-returning-learner-profile-refresh` | PASS |

---

## Results by skill

### smart-init (new this pass)

#### 01-clean-first-onboarding — PASS

**Why:** Every expected behavior (repo inference for stack, six-question ceiling, one question per turn, schema-exact profile, single closing suggestion) is named explicitly in SKILL.md. Inference-before-asking is the load-bearing rule and it's stated directly.

**Risk notes:** None specific.

#### 02-learner-volunteers-most-context — PASS

**Why:** *"Never re-ask what you already know"* and *"Drop any question whose answer you can confidently infer"* cover this case directly. The instruction is explicit.

**Risk notes:** Number-of-confirmations is judgement-dependent — the model could over-confirm even when the learner has volunteered cleanly. Not severe; the instruction is clear.

#### 03-learner-pushes-back-on-process — PASS WITH RISK

**Why:** SKILL.md's "Don't insist; profile is optional, not a gate" framing addresses this. Pragmatic exit paths are honored in principle.

**Risk:** The skill's pushback guidance is mostly written for **style preferences** ("just give me direct answers") rather than **process refusal** ("just skip this entirely"). The boundary between "pragmatic acknowledgment" and "lecturing or capitulating" is judgement-dependent. There is also no explicit guidance on whether a partial profile should be written when the learner stops mid-flow — the eval expects this to depend on explicit consent.

**Recommended fix:** Add explicit guidance for *process refusal* (vs. style preference): brief acknowledgment + ask whether to save what's answered so far + close. Make the "don't write silently-incomplete profiles" rule explicit.

#### 04-returning-learner-profile-refresh — PASS

**Why:** Step 3 (update pass) is explicitly designed for this case. Summarize-existing-back, single open question, cap follow-ups at 3 — all named in SKILL.md.

**Risk notes:** None specific.

---

### smart-explain

| Scenario | Verdict |
| --- | --- |
| `01-clean-symbol-explanation` | PASS |
| `02-ambiguous-file-scope` | PASS |
| `03-adversarial-dump-demand` | PASS WITH RISK |
| `04-trivially-simple-code` | **PASS** *(upgraded)* |

#### 04-trivially-simple-code — PASS (upgraded from PASS WITH RISK)

**Why upgraded:** SKILL.md now includes a dedicated *"Compressed shape for trivial or obvious code"* sub-section in the Operational Flow, covering Steps 1 through 7. Compression is no longer a Step 2 caveat — it's a session shape.

**Residual risk:** Recognition that code *is* trivial is still judgement-dependent. The model has to correctly classify the target before applying the compressed shape. If misclassified (treating a moderately complex function as trivial, or vice versa), behavior diverges.

#### 03-adversarial-dump-demand — PASS WITH RISK (unchanged)

**Why:** SKILL.md addresses the case (*"Even when the learner asks 'just explain it,' begin with one thinking prompt... If they push back, stay light"*), but the compression-vs-collapse boundary is judgement-dependent.

**Recommended fix (unchanged from first pass):** Add explicit guidance on which steps compress and which stay under pushback. The trivial-code section added this pass is a model — adversarial pushback could use a similar named shape.

---

### smart-debug

| Scenario | Verdict |
| --- | --- |
| `01-stack-trace-with-hypothesis` | PASS |
| `02-vague-symptom` | PASS WITH RISK |
| `03-frustrated-learner-demanding-patch` | PASS |
| `04-broad-target-no-clear-cause` | **PASS** *(upgraded)* |

#### 04-broad-target-no-clear-cause — PASS (upgraded from PASS WITH RISK)

**Why upgraded:** Two prominent warnings now sit inside `failure-patterns.md` — one at the top of the document and one immediately before the symptom→candidates cheat sheet. Both name the entries as *candidate hypotheses requiring falsification*, not verdicts. The "symptom similarity is not proof" line is explicit.

**Residual risk:** The warnings reduce the temptation but cannot guarantee the model heeds them under pressure. A confidently-asserted catalog match under low evidence remains the most consequential potential failure mode this skill could exhibit. Real-session testing is the next-step validation; instruction-level review can't go further than this.

#### 02-vague-symptom — PASS WITH RISK (unchanged)

**Why:** *"Ask one or two short questions"* is in SKILL.md but the cap isn't enforced and the model might ask three or four if symptoms remain vague.

**Recommended fix (unchanged):** Make the "max 2 clarifying questions before proceeding with scaffolding" rule explicit.

---

### smart-test-me

| Scenario | Verdict |
| --- | --- |
| `01-concrete-code-target` | PASS |
| `02-pure-concept-target` | PASS |
| `03-adversarial-multiple-choice-request` | PASS |
| `04-acing-everything` | PASS WITH RISK |

**Internal-contradiction status:** *resolved.* The first bullet of *"What you must not do"* now aligns with the hard pedagogical constraint above it. The rule reads consistently across both locations:

- Generation-based retrieval is the default.
- Recognition prompts are allowed only as narrow scaffolding for a clearly blocked learner.
- Recognition is never the session's primary format.
- User requests to switch the session to MCQ / trivia / flashcards must still be declined.

**Note:** `references/question-patterns.md` still uses the categorical "Never use" language in its *"What not to use"* section. This is consistent if read as *"never use as the session's primary format"* — but the language is not literally consistent with the narrow-scaffolding exception. This is a minor wording drift, not a contradiction. Worth fixing eventually; not blocking.

#### 04-acing-everything — PASS WITH RISK (unchanged)

**Why:** *"Step difficulty up"* is named but vague. The difficulty-escalation move is judgement-dependent.

**Recommended fix (unchanged):** Add an explicit difficulty-escalation ladder — concrete rungs (edge cases → unusual prediction → critique → architectural transfer) reduce judgement load.

---

### smart-refactor

| Scenario | Verdict |
| --- | --- |
| `01-clear-smell` | PASS |
| `02-ugly-but-actually-fine` | PASS |
| `03-pattern-worship` | PASS |
| `04-just-rewrite-it` | PASS WITH RISK |

No changes this pass. See first-pass report for details. Residual risk on eval 04 is the anti-overreach behavior (model produces a "clean rewrite" with pattern moves the original didn't need). Judgement-dependent.

---

## Cross-skill findings

### Recurring strengths (unchanged from first pass)

- Transfer required across all four downstream skills.
- "Just give me the answer" handled with framework preservation.
- Profile optionality consistently respected.
- Misconception/incomplete-knowledge/uncertainty trichotomy operative.
- "Leave it alone" / "we don't know yet" as legitimate outcomes.

### Recurring risks (updated)

- **Compression-vs-collapse remains the largest residual cross-skill risk.** It surfaces in `smart-explain/03` (adversarial dump demand), `smart-debug/03` (frustrated patch demand — currently PASS, but with similar judgement load), and `smart-refactor/04` (just-rewrite-it overreach). The trivial-code session shape added to smart-explain this pass is a model for the fix: explicit naming of which steps compress and which stay. This pattern should be applied to the adversarial-pushback cases as well.
- **Catalog-temptation reduced but not eliminated.** Warnings in `failure-patterns.md` close the instruction gap. The behavioral risk — model pattern-matching from the catalog under low evidence — depends on the model heeding the warnings under pressure. Cannot be further verified at the instruction level.
- **Judgement-dependent calibration is the recurring shape of the remaining PASS WITH RISK verdicts.** When is code "trivial enough" (smart-explain), when is the symptom "clear enough" (smart-debug), when has the learner "aced enough" (smart-test-me), when does pushback warrant exit (smart-init), when does a rewrite stay scoped (smart-refactor) — each requires the model to make a perception call that the instructions cannot enforce.

### Contradictions (resolved)

- smart-test-me categorical-vs-exception: **resolved this pass.** No remaining cross-document contradictions.

### Overly rigid behavior (unchanged)

- Pre/post confidence brackets in casual smart-test-me interactions.
- Cargo-cult challenge potentially over-firing on well-motivated pattern reaches in smart-refactor.

### Trigger / invocation uncertainty (unchanged, still foundational)

- All eval scenarios assume explicit slash invocation. Whether Claude Code's skill selection actually triggers as the descriptions describe remains unverified. The "do not auto-invoke" hedging is an architectural assumption.

---

## Recommended follow-ups

Re-ranked after this polish pass. Six remaining recommendations; four addressed.

| # | Recommendation | Status |
| --- | --- | --- |
| — | Address catalog-temptation in smart-debug | **addressed** (warnings added) |
| — | Resolve smart-test-me categorical-vs-exception contradiction | **addressed** |
| — | Extend trivial-code guidance in smart-explain beyond Step 2 | **addressed** |
| — | Add `evals/` to smart-init | **addressed** (4 scenarios) |
| 1 | **Field-test the trigger mechanism in real Claude Code.** | open — foundational |
| 2 | **Tighten adversarial-compression guidance** (smart-explain/03 and the sibling cases in smart-debug/03 and smart-refactor/04). Apply the same approach used for the trivial-code session shape: name explicitly which steps compress and which stay. | open |
| 3 | **Add a difficulty-escalation ladder to smart-test-me.** Concrete rungs (edge cases → unusual prediction → critique → architectural transfer) for the "step difficulty up" case. | open |
| 4 | **Add explicit guidance for process-refusal vs. style-preference pushback in smart-init.** Brief acknowledgment + ask whether to save partial answers + close. Make the "don't write silently-incomplete profiles" rule explicit. | open (new this pass) |
| 5 | **Strengthen intent-discovery in smart-refactor for performance/hot-path cases.** Add an explicit prompt to ask about performance concerns when the code shape suggests them. | open |
| 6 | **Re-run as a session-level review against real transcripts.** Instruction review can't go further. This is now the single highest-leverage next step. | open — most valuable next |

---

## Honest framing

The verdict numbers improved (10 → 15 PASS) because the instruction gaps that triggered the previous PASS WITH RISK verdicts were closed. The underlying behavioral risks — pattern-matching under low evidence, over-compressing under pushback, judgement calls on perception — are reduced but not measured. The single highest-leverage next step is no longer instruction-level work; it's running real sessions and grading the model's actual behavior against these contracts.
