# Concept bridging

This document is the catalog of programming concepts that `smart-explain` commonly bridges to, how to recognize each pattern in code, and the laddering between concrete implementation and abstract idea.

Use it when you've identified that some code embodies a concept worth naming and you want a sharper bridging phrase or a recognition checklist.

The catalog is not exhaustive. The skill of bridging is recognizing *that* an underlying idea exists; the catalog just helps with *which* one and *how* to phrase it.

---

## How bridging works

A bridge is a one-sentence move that does three things at once:

1. **Names the concept** — *"this is dependency inversion."*
2. **Anchors it to what the learner just saw** — *"…because the function takes a client *interface*, not a concrete HTTP client."*
3. **Implies what the learner can carry away** — *"…which means we can swap the client without touching this function."*

The bridging phrase is **one sentence**. Anything more becomes a lecture. The follow-up question is where the depth happens: *"Where else in this codebase do you see code that takes an interface like this?"*

---

## When to bridge — and when not to

Bridge when **all three** are true:

- The pattern is genuinely present, not a stretch.
- The learner is following the implementation. (Don't bridge over confusion — fix the confusion first.)
- The concept has carry: it will help the learner read or write *other* code, not just this one function.

Do not bridge:

- To show off vocabulary.
- When the "pattern" is just normal code with a fancy name.
- When the learner is in cognitive overload and adding a new term will make it worse.

A session with two well-placed bridges beats a session with seven that overwhelm.

---

## The catalog

For each concept: a one-line definition, a recognition checklist, the bridging phrase, and a transfer-question hook for Step 7.

### Dependency injection / inversion

- **One-liner:** the function depends on an abstraction it's given, not a concrete thing it creates.
- **Recognize:** the function takes a client / repository / service as an argument or constructor parameter, instead of importing and instantiating one.
- **Bridge:** *"This is dependency injection — the caller decides which client to use, so this function doesn't know or care about the concrete implementation."*
- **Transfer hook:** *"What would change about testing this if the function created its own client instead of receiving one?"*

### Immutability

- **One-liner:** values aren't modified in place; transformations produce new values.
- **Recognize:** `const` / `readonly` / frozen objects, spread or clone operations returning new structures, no in-place mutations of shared data, return values rather than mutated arguments.
- **Bridge:** *"Notice how nothing here mutates the input — the function returns a new object instead. That's immutability, and it's what makes this safe to call from anywhere without surprising other callers."*
- **Transfer hook:** *"What would have to change in callers if this function *did* mutate the input?"*

### Pure functions

- **One-liner:** same input → same output, no side effects.
- **Recognize:** no I/O, no clock, no random, no shared state — just a transformation from arguments to return value.
- **Bridge:** *"This is a pure function — given the same input it always produces the same output, and it doesn't touch anything outside itself. That's why this one is so easy to test."*
- **Transfer hook:** *"Which of the other functions in this module are pure, and which aren't? What forces the impure ones to be impure?"*

### Functional core, imperative shell

- **One-liner:** the messy I/O is at the edges; the logic in the middle is pure.
- **Recognize:** a thin top-level function that reads inputs, calls a pure transformation, and writes outputs — with the transformation being trivially testable on its own.
- **Bridge:** *"Notice the shape: I/O at the top, pure logic in the middle, I/O at the bottom. That's a 'functional core, imperative shell' — the testable part is sandwiched between the messy parts."*
- **Transfer hook:** *"Where in this codebase does this shape break down — where is logic tangled with I/O?"*

### Pipeline / transformation chain

- **One-liner:** data flows through a sequence of stages, each doing one thing.
- **Recognize:** `.map(...).filter(...).reduce(...)`, or sequential function calls where the output of each becomes the input of the next, or a compose / pipe utility.
- **Bridge:** *"This is a pipeline — each stage transforms the data once and passes it on. The shape lets you read top-to-bottom, like a recipe."*
- **Transfer hook:** *"If we needed to add a new transformation in the middle, where would it slot in — and what would *not* need to change?"*

### Closures and captured state

- **One-liner:** a function carries its surrounding scope with it.
- **Recognize:** a function returns another function that references variables from the outer scope; factory functions that "remember" configuration.
- **Bridge:** *"This is a closure — the returned function still has access to the variables that existed when it was created. That's how it 'remembers' the config without you having to pass it every time."*
- **Transfer hook:** *"What's the difference between a closure that captures a variable and a class with that variable as a field?"*

### Async control flow

- **One-liner:** the function suspends and resumes rather than running straight through.
- **Recognize:** `async` / `await`, Promises, futures, callbacks, `then`-chains, generators, coroutines.
- **Bridge:** *"When `await` shows up, control flow isn't straight-line anymore — this function suspends until the promise resolves, and other code can run in between. That's why you can't reason about it like a synchronous function."*
- **Transfer hook:** *"What could go wrong if two callers triggered this function at almost the same time?"*

### Idempotency

- **One-liner:** doing it twice has the same effect as doing it once.
- **Recognize:** *"if exists, do nothing"* logic; upserts; PUT semantics; deduplication via an idempotency key; retry-safe operations.
- **Bridge:** *"This is idempotency — running it twice gets you the same end state as running it once. That's what makes it safe to retry on failure."*
- **Transfer hook:** *"Which operations in this codebase are *not* idempotent, and what's the consequence if one of them gets retried?"*

### Invariants and guards

- **One-liner:** something the rest of the code is allowed to assume.
- **Recognize:** early validation that throws or returns on bad input; assertions; precondition checks at the top of a function; *"if not X, bail"* patterns.
- **Bridge:** *"This guard is protecting an invariant — once we get past line N, the rest of the function can assume the input is valid. That's why the body doesn't have to keep re-checking."*
- **Transfer hook:** *"If we deleted this guard, where else in the function would have to start defending itself?"*

### Separation of concerns

- **One-liner:** each piece is responsible for one thing.
- **Recognize:** functions or modules with focused responsibilities; calls that delegate rather than do everything inline; thin orchestration layers calling specialized helpers.
- **Bridge:** *"This function isn't doing the work itself — it's coordinating. Each helper has one job. That's separation of concerns, and it's what makes each piece independently understandable and testable."*
- **Transfer hook:** *"If we needed to change *how* we hashed passwords, how many functions would we have to touch?"*

### Error propagation strategies

- **One-liner:** how a failure travels from where it occurred to where it's handled.
- **Recognize:** thrown exceptions vs. `Result` / `Either` types vs. error-as-value vs. callbacks; try/catch shapes; error wrapping or re-raising.
- **Bridge:** *"Notice that this function doesn't try to handle the error — it lets it bubble up to the caller. That's deliberate: this layer doesn't know enough to decide what 'handle' even means."*
- **Transfer hook:** *"Where in this codebase do errors *stop* — what's the boundary that decides how to react?"*

### Caching and memoization

- **One-liner:** trade memory for time by remembering past results.
- **Recognize:** lookup tables keyed by input; LRU structures; *"check, compute, store"* patterns; memoization decorators.
- **Bridge:** *"This is caching — we pay memory to skip recomputation. The interesting design decisions are *when* to invalidate and *what* counts as 'the same input.'"*
- **Transfer hook:** *"What would happen if the underlying data changed but the cache didn't know?"*

### State machines

- **One-liner:** the system is in exactly one of a finite set of states, and only certain transitions are legal.
- **Recognize:** a `status` field with a small enum; switch statements over states; transition functions; *"you can only X from state Y"* rules.
- **Bridge:** *"This is a state machine — the entity is in one of a fixed set of states, and only some transitions are allowed. The rules about what can happen when live in the transitions, not in scattered if-checks."*
- **Transfer hook:** *"What state is missing from this model — what's a real-world case that doesn't fit?"*

### Transactional boundaries

- **One-liner:** a group of operations either all happen or none do.
- **Recognize:** explicit transaction wrappers; unit-of-work patterns; *"save everything together or fail together"*; outbox patterns.
- **Bridge:** *"This is a transactional boundary — these operations are bundled so we never see a half-finished state from outside. If any of them fails, the whole bundle rolls back."*
- **Transfer hook:** *"What's the consequence if this *weren't* transactional — what intermediate state could a reader observe?"*

### Defensive copying

- **One-liner:** make a copy because you don't trust callers (or because callers shouldn't trust you).
- **Recognize:** clone of an input before storing it; returning a clone of an internal collection instead of the original.
- **Bridge:** *"Notice the copy here — this protects internal state from being mutated by the caller after the fact. It's the imperative-language version of immutability."*
- **Transfer hook:** *"What bug surface opens up if we remove that copy?"*

### Composition over inheritance

- **One-liner:** build behavior by combining small pieces, rather than by extending a base.
- **Recognize:** small focused objects/functions wired together; no deep type hierarchies; behaviors plugged in via parameters or fields.
- **Bridge:** *"Instead of subclassing to extend behavior, this code wires behavior in from outside. That's composition — the behavior the function ends up with is decided by what you hand it, not by which class it inherits from."*
- **Transfer hook:** *"What would the inheritance-based version of this look like, and where would it become painful first?"*

### Layered architecture / boundary discipline

- **One-liner:** the codebase is organized into layers, and each layer is only allowed to talk to specific others.
- **Recognize:** explicit folders or modules for domain / application / infrastructure / interface; imports that flow in one direction; rules against (say) the domain layer importing from infrastructure.
- **Bridge:** *"This function lives in the domain layer — notice it doesn't import anything from infrastructure. That one-way import rule is what keeps the business logic testable in isolation."*
- **Transfer hook:** *"What would happen to testability if domain code started importing from infrastructure?"*

---

## Laddering: concrete → abstract → other concrete

The most durable bridges follow this shape:

1. **Concrete (here):** *"In this function, the caller passes in the database client instead of the function creating one."*
2. **Abstract:** *"That's dependency injection — the function depends on what it's given, not on what it constructs."*
3. **Other concrete (transfer):** *"Where else in this codebase do you see functions taking their collaborators as arguments rather than importing them?"*

The laddering is what creates transfer. A pure abstract definition (*"DI means…"*) is a vocabulary lesson, not a learning event. The two concrete anchors (one in front of the learner, one somewhere else) are what stitch the concept to memory.

---

## When you can't find a concept

Sometimes there isn't a clean concept hiding in the code. The function just does what it does, in a straightforward way. That's fine — don't manufacture a pattern. Bridge to *taste* or *style* instead:

- *"Notice how the function reads top to bottom without nested branching — that's just careful local style, but it's the kind of code that ages well."*
- *"This is one of those functions that's boring on purpose — the surprise here is that there are no surprises."*

Naming the absence of cleverness is a real teaching moment for learners who are over-rotating on patterns.

---

## Mirroring the learner's stack

When the profile records a primary stack, prefer analogies from inside that stack to generic ones. Some examples of the same concept, three different framings:

- **Dependency injection, to a backend engineer:** *"Same idea as constructor-injecting a repository in a service."*
- **Dependency injection, to a frontend engineer:** *"Same idea as passing a fetcher into a hook instead of importing one inside the hook."*
- **Dependency injection, to a data engineer:** *"Same idea as passing the connection object into your transformation instead of opening one inside it."*

Mirroring the learner's vocabulary turns a concept they might dismiss as *"OO patterns"* into something they recognize from their own working day. That's what makes the bridge stick.
