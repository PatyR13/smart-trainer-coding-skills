# Code smells

This document is the catalog of design smells the skill commonly engages with. For each smell:

- **One-liner** — what it is.
- **Recognition cues** — what to look for.
- **Plausible intent** — reasons the code might be shaped this way that aren't mistakes (Step 1 of the framework).
- **Candidate refactors** — moves to consider, with the cost of each.
- **Common misconception** — what learners typically get wrong about this smell.
- **When it's actually fine** — critical, and often skipped in other catalogs. A smell is a *signal*, not a *verdict*.

The catalog is not exhaustive. The skill of refactoring is recognizing *that* a smell is present; the catalog just provides starting points, recognition cues, and — most importantly — the discipline to ask whether the smell really wants a refactor in this specific context.

---

## How to use this catalog

When working through Step 2 of the refactoring framework, scan for two or three smells. Don't enumerate all fifteen. Pick the ones that seem most consequential.

For each smell you identify, walk the learner through:

1. The recognition cue you used.
2. A plausible intent reading (*"this might exist because…"*).
3. One or two candidate refactors with their costs.
4. Whether, in this specific case, the smell is actually a problem worth fixing.

That fourth question is the one this catalog enables you to answer well. Each entry below explicitly names when the smell is fine.

---

## The catalog

### Long function

- **One-liner:** a function whose body is large enough that you can't keep all of it in your head at once.
- **Recognition cues:** scrolling required to read it; multiple distinct "phases" inside one body; comments dividing it into sections (those comments are often unconscious extraction hints).
- **Plausible intent:** sequential clarity (a single straight-line story is sometimes easier to follow than a chain of helpers); debuggability (fewer stack frames); performance (avoiding call overhead in hot paths); explicitness in a domain where understanding the full flow matters.
- **Candidate refactors:** extract method (cost: more files/functions to open, less context in each); inline helpers (cost: longer body); compose-method pattern (cost: cognitive overhead of naming each phase).
- **Common misconception:** *"functions should be short"* as a universal rule. There's no length that's automatically too long. The question is whether the length is *carrying its weight*.
- **When it's actually fine:** when the function tells a single sequential story that's harder to follow when fragmented. Many init functions, top-level workflow orchestrators, and DSL-like configurations read better long.

### Duplication (actual)

- **One-liner:** the same logic appears in two or more places, and a change to one should require a change to the others.
- **Recognition cues:** identical or near-identical blocks; the *same bug* would need to be fixed in multiple places.
- **Plausible intent:** the duplication might not be real — two pieces of code that *currently* look alike but represent different concepts that will diverge.
- **Candidate refactors:** extract shared function (cost: a new dependency relationship); pull up to a common parent (cost: coupling between the previously-independent callers); template method (cost: indirection and inheritance).
- **Common misconception:** assuming visual similarity equals logical duplication. The DRY principle is about *knowledge*, not *characters*.
- **When it's actually fine:** when the two pieces of code represent *different* concepts that *happen* to look alike. Premature de-duplication is one of the most expensive refactoring mistakes — it couples two things that should have stayed independent.

### Mixed responsibilities (God function/class)

- **One-liner:** a single unit handles several distinct concerns (validation + business logic + I/O + logging + …).
- **Recognition cues:** the unit changes for multiple unrelated reasons; testing it requires mocking many things; the name uses "and" or is unspecific (*"processOrder"*, *"handleRequest"*).
- **Plausible intent:** delivery pressure; a top-level orchestrator legitimately coordinating several concerns; explicitness in a small codebase where indirection would cost more than it'd buy.
- **Candidate refactors:** responsibility separation (cost: more files, more vocabulary, possibly thinner abstractions); extract the side effects to the edges (functional-core / imperative-shell, cost: structural change); orchestrator-thinning (keep the orchestrator, push each concern into its own helper — cost: indirection).
- **Common misconception:** treating every multi-concern function as a violation. Top-level orchestrators *should* coordinate multiple concerns; they just shouldn't *do* all of them inline.
- **When it's actually fine:** small scripts, one-off tools, top-level entry points that legitimately need to see and orchestrate multiple concerns.

### Hidden coupling

- **One-liner:** changing one piece of code requires changing another in a way that isn't obvious from the code structure.
- **Recognition cues:** *"if you change X, also remember to change Y"* lore that lives in people's heads; tests that fail in seemingly unrelated places when the code changes; comments saying *"keep in sync with…"*.
- **Plausible intent:** sometimes coupling really is intrinsic to the problem (a serializer and deserializer, two ends of a protocol). Sometimes the duplication-or-coupling tradeoff was deliberately resolved toward duplication.
- **Candidate refactors:** make the coupling explicit (a shared type, a single source of truth, a shared module — cost: a new dependency or a new file); consolidate the duplicated knowledge (cost: see "duplication" — premature consolidation has its own risks); add tests that fail loudly when the invariant breaks (cost: more tests, but the *cheapest* fix here).
- **Common misconception:** that the goal is to eliminate the coupling. Sometimes the coupling is inherent; the goal is to *make it visible*, not erase it.
- **When it's actually fine:** when the coupling is properly documented and the surface is small enough that the lore is reliable.

### Temporal coupling

- **One-liner:** method A must be called before method B, but nothing in the code enforces or signals this.
- **Recognition cues:** *"call `init` first"*, *"set this before that"*, runtime errors when methods are called in the wrong order, sequences like `connect()` → `authenticate()` → `request()` that have to happen in that order.
- **Plausible intent:** the API mirrors the underlying protocol; the alternative (combining everything into one call) loses useful intermediate state.
- **Candidate refactors:** combine into a single call when the steps are always sequential (cost: less flexibility for advanced callers); use the *builder* shape where the type system enforces the order (cost: a new type / pattern); document the contract clearly (cheapest, but doesn't prevent misuse).
- **Common misconception:** that temporal coupling can always be eliminated. Sometimes it's protocol-inherent (handshake, retries, transactions).
- **When it's actually fine:** when the order is obvious from the API names and the call sites are few. A builder pattern for a function that's called in three places is overkill.

### Unclear naming

- **One-liner:** variables, functions, or types whose names don't tell you what they are.
- **Recognition cues:** `data`, `info`, `manager`, `handler`, `process`, `do`, `temp`, `obj`, single-letter non-iterator variables, names that require reading the body to understand.
- **Plausible intent:** rapid prototyping; legacy; convention from another language or ecosystem.
- **Candidate refactors:** rename (cost: diff noise; potential merge conflicts; sometimes a domain term doesn't exist and naming becomes design work itself).
- **Common misconception:** that longer names are always better. *"customerOrderProcessingRequest"* isn't more useful than *"order"* in a context where everything is about customer orders.
- **When it's actually fine:** when the naming follows codebase conventions consistently — even imperfect conventions are usually better than partial renames. Also when the scope is small enough that the name doesn't need to carry much.

### Mutation hazards

- **One-liner:** shared mutable state where mutation by one caller can surprise another.
- **Recognition cues:** functions that take an object and modify it in place; module-level mutable state; collections that are returned to callers without defensive copies.
- **Plausible intent:** performance (mutation avoids allocation in hot paths); historical design (the codebase predates immutability conventions); language idioms that lean toward mutation.
- **Candidate refactors:** return new values instead of mutating arguments (cost: allocations, possibly significant in hot paths); defensive copy on input/output boundaries (cost: allocations, but localized); freeze the object after construction (cost: language-dependent and incomplete protection).
- **Common misconception:** that mutation is always bad. Mutation localized inside a function is fine; mutation that crosses function boundaries is the problem.
- **When it's actually fine:** when the mutating function clearly *owns* the data (it created it, no one else has a reference) or the performance requirement is real and the contract is documented.

### Defensive complexity

- **One-liner:** excessive validation, null-checking, or fallback logic, often guarding against cases that can't actually happen.
- **Recognition cues:** null checks on values that come from validated sources; try/catch around code that can't throw; "just in case" branches with no documented case.
- **Plausible intent:** a real prior bug led to the defense — and the defense should stay; the function is at a system boundary where the input is genuinely untrusted; defensive programming culture from a more hostile codebase.
- **Candidate refactors:** remove unnecessary checks (cost: zero if the checks really are unnecessary; *catastrophic* if you remove a guard that was load-bearing because of a bug you didn't know about); push validation to the boundary, trust internals (cost: requires confidence about the boundary); document why the check exists if you keep it.
- **Common misconception:** that all defensive code is bad. Defensive code at *system boundaries* is good; defensive code redundantly applied at every internal call is the waste.
- **When it's actually fine:** at boundaries; when there's a documented prior incident that justifies the guard; when the cost of being wrong is high enough that paranoia is justified (auth, money, deletes).

### Large conditionals

- **One-liner:** sprawling if/else chains or switch statements that branch on type, state, or kind.
- **Recognition cues:** more than three or four cases; type-checking conditionals; switch statements on enum-like values that the codebase touches in multiple places.
- **Plausible intent:** the alternatives (polymorphism, dispatch tables) are heavier and not justified by the number of cases; the cases really are unrelated and shouldn't share an abstraction.
- **Candidate refactors:** replace with polymorphism (cost: more types/classes, indirection, scattered behavior); table dispatch (cost: indirection, but flatter than polymorphism); break each branch into its own function and call them from a small dispatcher (cost: function count); leave alone (cost: zero, if the conditional is bounded and stable).
- **Common misconception:** that all type-based conditionals should be polymorphism. For two or three cases, a conditional is often clearer; the polymorphic version is over-engineered.
- **When it's actually fine:** when the cases are stable, few, and unlikely to grow; when the alternative scatters behavior across files in a way that hurts more than the conditional.

### Primitive obsession

- **One-liner:** using language primitives (string, int) where a domain type would carry more meaning.
- **Recognition cues:** strings that semantically aren't strings (`email`, `userId`, `currencyCode`); IDs of different things that can be accidentally swapped; numbers without units.
- **Plausible intent:** language doesn't support cheap newtypes; the team's typing discipline is light; the domain is small enough that the extra types aren't carrying weight.
- **Candidate refactors:** newtype / branded type (cost: language-dependent ergonomics; sometimes requires casting at boundaries); value object class (cost: heavier, especially in languages where this means allocation); validation at boundaries with primitives internally (cost: leaks the primitive but isolates the validation).
- **Common misconception:** that every domain concept needs a type. A `string` that holds a name is *fine*; a `string` that holds an opaque ID that gets confused with other opaque IDs is the problem.
- **When it's actually fine:** when the primitive is used in a small scope where confusion is unlikely; when the language makes wrapper types painful enough to negate the benefit.

### Leaky abstraction

- **One-liner:** an abstraction that exposes its implementation details, so callers have to know how it works internally to use it correctly.
- **Recognition cues:** callers handling errors that should have been encapsulated; callers checking internal types or states; *"to use this correctly, you also need to know that…"*.
- **Plausible intent:** performance (full encapsulation costs allocation or indirection); incremental abstraction (the abstraction is half-built); pragmatic concessions to callers' real needs.
- **Candidate refactors:** seal the abstraction (cost: less flexibility, possibly missing real caller needs); push the leak into the type signature (cost: ugliness, but at least the leak is visible); accept the leak and document the contract (cost: ongoing discipline).
- **Common misconception:** that all leaks should be sealed. Some leaks are *features* (the caller really does need that internal detail to make decisions).
- **When it's actually fine:** when the leak is documented, the callers genuinely need the leaked detail, and sealing it would force callers into worse workarounds.

### Feature envy

- **One-liner:** a method that uses another object's data more than its own.
- **Recognition cues:** a method that reads or writes lots of fields of another object; *".other.x, .other.y, .other.z"* dotted access patterns; logic that "belongs" elsewhere.
- **Plausible intent:** the data really does live in two related places; the design predates the current logic; the alternative (moving the method) would create circular dependencies.
- **Candidate refactors:** move the method to where the data lives (cost: changes the method's home; possibly affects callers); introduce a domain method on the data-owning object (cost: more methods on that object).
- **Common misconception:** that feature envy is always a bug. Sometimes the calling code legitimately needs to combine data from multiple sources.
- **When it's actually fine:** at orchestrator layers that legitimately compose data from elsewhere; in DTO-style code where the "envy" target is just a data holder.

### Data clumps

- **One-liner:** the same group of variables passed around together in many places.
- **Recognition cues:** the same three or four parameters appearing in many function signatures; tuple-like return values that always have the same shape.
- **Plausible intent:** the variables aren't actually a unit — they just happen to appear together for now and will diverge later; introducing a type was deferred.
- **Candidate refactors:** introduce a type / struct / class for the clump (cost: more types, possibly more allocation, change to every call site); leave them as separate parameters if the clump is shallow (cost: parameter list length, possible misordering).
- **Common misconception:** that any repeated group must become a type. Some clumps are just locally co-occurring; making them a type adds noise without adding meaning.
- **When it's actually fine:** when the clump is shallow (two or three values used in a handful of places); when grouping them into a type would conflate concepts that should stay independent.

### Premature abstraction

- **One-liner:** an abstraction with one user, introduced "for flexibility" or *"in case we need it later."*
- **Recognition cues:** an interface with one implementation; a factory that builds one product; a "strategy" with one strategy; a config option that's never set differently.
- **Plausible intent:** the second user was planned and got cut from scope; the design was inherited from a place where the abstraction was warranted.
- **Candidate refactors:** inline the abstraction (cost: if a second user does appear, the abstraction has to be re-introduced; but YAGNI almost always wins here); leave it (cost: ongoing complexity for a benefit that may never arrive).
- **Common misconception:** that flexibility is free. Every abstraction is a bet that the flexibility it provides will be cheaper than what it costs. Most bets lose.
- **When it's actually fine:** when the second user is *imminent* and known, not hypothetical.

### Magic values

- **One-liner:** raw numbers, strings, or other literals embedded in logic where a named constant would clarify intent.
- **Recognition cues:** `if (status == 3)` instead of `if (status == STATUS_FAILED)`; `setTimeout(fn, 86400000)` instead of `setTimeout(fn, ONE_DAY_MS)`.
- **Plausible intent:** the values are obvious from context (rare); the constants live elsewhere and aren't worth re-importing for one use site.
- **Candidate refactors:** named constant (cost: trivial — this is one of the genuinely-low-cost refactors); typed enum (cost: more if the language requires more).
- **Common misconception:** that every literal needs a name. *"0"*, *"1"*, *"true"*, identity values in math — these often don't benefit from naming. The smell is about *meaningful* magic.
- **When it's actually fine:** when the value is genuinely self-explanatory in context (return statuses where 0 is the universal "ok"); when the constant would be used exactly once and naming it adds noise.

---

## When you can't find a smell

Sometimes the code is genuinely fine. The temptation is to find *something* to refactor, because the learner asked. Don't.

*"I don't see a meaningful smell here. The function is doing its job at a reasonable size, the naming is appropriate, the dependencies are clean. Is there a specific thing that prompted you to want to refactor this?"*

That sentence is the lesson. Refactoring without a reason is the cargo-cult failure mode in its purest form. A learner who can ask *"is there actually a problem to fix?"* before reaching for the refactor catalog has internalized the most important habit this skill teaches.
