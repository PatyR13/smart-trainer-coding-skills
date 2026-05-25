# Failure patterns

This document is the catalog of common bug shapes, organized so you can move from *symptom* → *candidate categories* → *first probe* without enumerating thirty possibilities.

Use it as a reference when narrowing the search space in Step 4, choosing a falsification probe in Step 5, or bridging the specific bug to a class the learner can recognize next time.

The catalog is not exhaustive. The skill of debugging is recognizing the shape; the catalog just provides good starting points and the conceptual frames that make each shape teachable.

---

## How to use this catalog

For each pattern below:

- **One-line description** of what the pattern is.
- **Recognition cues** — symptoms or evidence that point at this shape.
- **First probe** — the cheapest hypothesis-testing move when this shape is on the table.
- **Conceptual bridge** — the underlying idea worth naming for the learner.
- **Common misconception** — the wrong reasoning learners often apply here, useful for Step 5.

The bridges intentionally overlap with `smart-explain`'s `concept-bridging.md` (idempotency, invariants, etc.) — debugging is often the place those concepts get *learned*, because that's where their absence hurts.

---

## Patterns

### Input issue

- **What it is:** the code is fine; the input wasn't what the code assumed.
- **Recognition cues:** wrong output but no error; works in tests but not in production; works with example data but not with real data; new edge case (empty list, null, very long string, unicode).
- **First probe:** log the input at the function boundary and compare to what the code expects. Cheap, decisive.
- **Conceptual bridge:** *"This is the function meeting an input shape it wasn't designed for — the bug is in the contract, not the logic."*
- **Common misconception:** the learner is convinced the logic is wrong and re-reads it three times, never checking what the input actually was.

### State mutation (shared state changed unexpectedly)

- **What it is:** something the code thought was stable was modified by another caller, another thread, or the function itself.
- **Recognition cues:** behavior changes after subsequent calls; works in isolation, fails when chained; debugging output shows different values at different points within the "same" object.
- **First probe:** at the point of unexpected behavior, log the object identity and value. Then go back one step and check the same thing.
- **Conceptual bridge:** *"This is a shared mutable state bug — two parts of the code were both writing to the same thing without coordinating."*
- **Common misconception:** assuming a value passed by reference is "frozen" once it's handed off. Mutable references aren't a snapshot.

### Async timing / ordering

- **What it is:** operations completed in a different order than the code assumed.
- **Recognition cues:** intermittent behavior; works in dev (single user) and fails under load; works locally and fails in CI; race-like symptoms (data shows up, then doesn't); behavior changes when you add a `console.log` (timing perturbation).
- **First probe:** instrument with timestamps or sequence numbers at the suspected ordering points. Look for the order assumption that's wrong.
- **Conceptual bridge:** *"This is async ordering — the code assumed A finished before B, but the runtime didn't guarantee that."*
- **Common misconception:** treating `async` functions like they "happen in order" without realizing nothing was awaited.

### Race condition

- **What it is:** two concurrent operations both modified shared state, and the outcome depended on which got there first.
- **Recognition cues:** truly intermittent (~50/50 in a tight loop); only reproduces under load; gets harder to reproduce when you add logging; behaves differently in single-threaded vs. multi-threaded contexts.
- **First probe:** identify the shared resource. Ask: "what happens if two callers hit this at exactly the same time?" Then construct the smallest concurrent repro.
- **Conceptual bridge:** *"This is a race — there's a window where two operations interleave in a way the code didn't account for."*
- **Common misconception:** thinking the bug "isn't a real race" because it almost always works. Almost-always-works under low load is exactly the race signature.

### Stale closure

- **What it is:** a closure captured a variable at one point in time, and the rest of the code expects the *current* value, not the captured one.
- **Recognition cues:** UI state seems "stuck" at an old value; event handlers fire with old props/state; *"I'm setting the value but the handler sees the old one"*; classic React / Vue / Svelte symptoms.
- **First probe:** log the captured value vs. the current value at the point of use. The difference is the bug.
- **Conceptual bridge:** *"The closure is holding onto an old value — it captured what existed when the closure was created, not what exists now."*
- **Common misconception:** assuming closures "see live values." Closures see what they captured.

### Config mismatch / environment difference

- **What it is:** code is correct, but the environment it's running in has different config / env vars / feature flags / dependencies than the environment where it worked.
- **Recognition cues:** *"works on my machine"*; passes in CI, fails in prod; fails for one user, not another; same code, different binary outcome.
- **First probe:** diff the environments. Env vars, config files, library versions, feature-flag values. The bug is usually in the diff.
- **Conceptual bridge:** *"This isn't a code bug — it's a config bug. The code depends on something external that's different between environments."*
- **Common misconception:** assuming environments are equivalent because they share most of their config. The diff is exactly where bugs live.

### Serialization boundary

- **What it is:** data was serialized one way and deserialized another, losing or transforming information across the boundary.
- **Recognition cues:** values change as they cross a network boundary (API → client); dates become strings; numbers lose precision (large integers in JSON); enums round-trip as strings; deeply nested objects lose fields.
- **First probe:** log the value on both sides of the serialization boundary. The diff is the bug.
- **Conceptual bridge:** *"This is a serialization boundary bug — the two sides have different ideas about what the data shape is."*
- **Common misconception:** trusting that `JSON.parse(JSON.stringify(x))` gives you back `x`. It almost never does for non-trivial data.

### Dependency contract mismatch

- **What it is:** caller and callee disagree about what a function does — argument shape, return type, error semantics, retry behavior.
- **Recognition cues:** "the function returned undefined and I expected a value"; "this throws on empty input but I thought it returned null"; new behavior after a dependency upgrade; works for one shape of input, fails for another.
- **First probe:** read the dependency's actual code or docs, not your memory of them. Compare to what the calling code assumes.
- **Conceptual bridge:** *"This is a contract mismatch — the code calling this function had a different model of its behavior than the function actually has."*
- **Common misconception:** trusting that a function "still works the way it used to" after an upgrade, or after someone else edited it.

### API assumption mismatch (external service)

- **What it is:** the third-party API actually behaves differently than the calling code assumed — different status codes, different error shapes, different rate limits, different timing.
- **Recognition cues:** failures correlate with specific request patterns; intermittent 5xx that aren't documented; response shape changes for certain inputs; behavior differs between sandbox and prod environments.
- **First probe:** capture the actual request and response from the API. Compare to the docs and to what the code assumes.
- **Conceptual bridge:** *"This is an external contract bug — the API doesn't behave the way we believed it did."*
- **Common misconception:** trusting the API docs as ground truth. Docs lag.

### Environment difference (CI/prod/local)

- **What it is:** same code, different runtime environment — different OS, different filesystem, different timezone, different locale, different concurrency, different network.
- **Recognition cues:** passes locally / fails in CI; passes in CI / fails in prod; fails only on certain dates or in certain timezones; file paths or line endings behave differently.
- **First probe:** identify the simplest environmental difference. Run the failing test in the failing environment with extra logging.
- **Conceptual bridge:** *"This is environmental — the code is making assumptions about the runtime that don't hold everywhere it runs."*
- **Common misconception:** assuming "the test environment is basically the same" — but timezones, locales, filesystem case-sensitivity, and concurrency models all bite eventually.

### Off-by-one / boundary

- **What it is:** loop, slice, range, or index is off by exactly one element, or fails on the empty / single-element / maximal case.
- **Recognition cues:** works for normal inputs, fails for empty or single-element ones; first or last element is missing or duplicated; *"it almost works."*
- **First probe:** run the code with the boundary inputs: empty, one element, exactly N, exactly N+1.
- **Conceptual bridge:** *"This is a boundary bug — the logic handles the typical case but the edge cases violate the assumption."*
- **Common misconception:** testing only the typical case and concluding the logic is correct.

### Null / undefined propagation

- **What it is:** a value that's expected to be present is missing, and the absence flows through several function calls before causing a visible failure far from the source.
- **Recognition cues:** *"cannot read properties of undefined"* or equivalent; the stack trace points at a place the bug isn't actually located; defensive checks are scattered throughout the code.
- **First probe:** trace the missing value backward from where it was used. Where was it last definitely present?
- **Conceptual bridge:** *"This is null propagation — the absence started somewhere and traveled until it hit code that couldn't handle it. The fix is at the source, not the symptom."*
- **Common misconception:** patching the symptom site with a defensive check instead of asking why the value was missing in the first place.

### Encoding / charset

- **What it is:** the same byte sequence is being interpreted as a different character set on different sides of a boundary.
- **Recognition cues:** garbled characters; unicode characters that work in dev but break in prod; emojis truncated; passwords with special characters fail; CSV import produces nonsense for some rows.
- **First probe:** inspect the raw bytes on both sides of the boundary. Compare to the expected encoding.
- **Conceptual bridge:** *"This is an encoding boundary — bytes and characters aren't the same thing, and a boundary somewhere is converting incorrectly."*
- **Common misconception:** treating strings as if their byte length and character length are the same. UTF-8 says hello.

### Resource leak (memory, file handles, sockets, DB connections)

- **What it is:** something is allocated but never released, and usage grows over time.
- **Recognition cues:** works for a while, then degrades or fails; restart "fixes" it; profiler or monitoring shows a steady upward slope; *"runs out of X after N hours."*
- **First probe:** find what's not being released. Look for paired alloc/free patterns where the free is missing on some path (often the error path).
- **Conceptual bridge:** *"This is a resource leak — every acquisition needs a release on every path out of the function, including the error paths."*
- **Common misconception:** assuming the happy path's cleanup is enough. Error paths leak.

### Type coercion surprise

- **What it is:** a language's implicit type conversion produced an unexpected result.
- **Recognition cues:** comparisons return surprising results (`"0" == false`, `[] + {}` etc.); string concatenation where math was expected (or vice versa); silent truncation; date strings parsed in unexpected timezones.
- **First probe:** log the runtime type and value at the surprising point. The bug is usually visible in the type, not the value.
- **Conceptual bridge:** *"This is implicit type coercion — the language did a conversion the code didn't ask for, and the conversion picked a different interpretation than intended."*
- **Common misconception:** trusting `==` (or equivalent loose comparison) to do the obvious thing.

### Cache invalidation / staleness

- **What it is:** the system is reading from a cache that doesn't reflect recent writes.
- **Recognition cues:** *"I changed it, but it still shows the old value"*; works after restart or hard refresh; some users see new data and others don't; eventual consistency between replicas.
- **First probe:** bypass the cache and verify the source of truth has the new value. If yes, the bug is in invalidation. If no, the bug is upstream.
- **Conceptual bridge:** *"This is a cache invalidation bug — the cache and the underlying data disagree about what's current."*
- **Common misconception:** assuming "the cache will pick it up eventually" is acceptable for the user-facing case where it's not.

### Test bug (vs. code bug)

- **What it is:** the production code is correct; the test is wrong (or flaky on its own merits — bad setup/teardown, ordering dependency, hidden shared state).
- **Recognition cues:** test fails but you can't reproduce the symptom by running the code manually; test ordering matters; tests pass individually but fail when run together; tests pass on rerun.
- **First probe:** look at the test setup/teardown and shared fixtures, not the code under test. Try running the failing test in isolation.
- **Conceptual bridge:** *"The bug is in the test, not the code. The test isn't asserting what it claims to assert."*
- **Common misconception:** assuming the test is correct and the code is wrong. Sometimes it's the other way around — and confidently changing correct code to satisfy a wrong test introduces a real bug.

---

## Mapping symptoms to candidate patterns

A rough cheat sheet for Step 4 partitioning:

| Symptom shape | First candidates |
| --- | --- |
| Wrong output, deterministic | input issue, off-by-one, logic, dependency contract mismatch |
| Wrong output, intermittent | async timing, race, stale closure, cache staleness |
| Crash with "undefined" / null reference | null propagation, dependency contract mismatch |
| Flaky test | race, async timing, shared state between tests, test bug |
| "Works on my machine" | config mismatch, env difference, encoding |
| Slow / degrading over time | resource leak, cache miss explosion, N+1 query |
| Works for normal input, fails for edge | input issue, off-by-one |
| Works in unit test, fails in integration | dependency contract, env, serialization |
| New behavior after dependency upgrade | dependency contract mismatch, API assumption |

Treat the table as a starting partition, not a verdict. The point of Step 5 is to falsify the wrong ones quickly.

---

## When the bug doesn't match a pattern

Some bugs are genuinely novel — they don't fit any catalog entry. That's fine. Don't force a pattern. Run the framework anyway:

- Step 3 still applies — separate facts from assumptions.
- Step 5 still applies — every hypothesis still needs a falsification probe.
- Step 8 still applies — *"how could we prevent or detect this class of bug?"* is answerable even for novel shapes.

What the catalog buys you is *speed* on common shapes. The framework buys you *correctness* on all shapes, novel or not.
