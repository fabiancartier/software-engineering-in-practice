# Lab 15 — Patterns without pattern worship

## Story

Look back at Version B's discount-code file and the `Notifier`
implementations from Lab 14 in your chosen track. You built both before
learning their "official" name. It turns out there is one.

## Learning objectives

After this lab you should be able to:

- Recognize the Strategy pattern in code you already wrote, before
  being told its name.
- Explain Dependency Injection using a function you already wrote
  (`send_receipt_ready`, `SendReceiptReady`, or `sendReceiptReady`)
  rather than a definition.
- Explain, in one sentence each, what a Factory and an Adapter are for.

## Before you start

- Complete Labs 12-14 in your chosen track.
- You don't need any new tools. This lab revisits code you already wrote.

## Your task

1. Reread your chosen track's Version B discount-code file and its
   `apply`-style function, and the `Notifier` implementations plus the
   `send_receipt_ready`-style function from Lab 14. In a notes file
   `labs/15-patterns-without-worship/my-notes.md`, write, in your own
   words, what these two pieces of code have in common. Focus on
   how each one avoids a long conditional chain to pick a behavior.

### Python

- `examples/discount-codes/version-b/python/billing/discount_codes.py`
- `examples/notifier/python/notifier/notifier.py`

### Go

- `examples/discount-codes/version-b/go/billing/discount_codes.go`
- `examples/notifier/go/notifier.go`

### Java

- `examples/discount-codes/version-b/java/src/main/java/billing/DiscountCodes.java`
- `examples/notifier/java/{Notifier,ConsoleNotifier,InMemoryNotifier,ReceiptService}.java`

2. This structure has a name. Several interchangeable implementations
   of the same small contract are selected by the caller instead of
   being built into one large conditional. This is the **Strategy**
   pattern. Each entry in your discount-code map or dictionary is a
   strategy. `ConsoleNotifier` and `InMemoryNotifier` (and now your
   `SilentNotifier`) are each a strategy for delivering a notification.
   Strategy doesn't require a class hierarchy. In Go and Python, a
   plain function stored in a map *is* a strategy; you don't need to
   wrap it in a type.
3. `send_receipt_ready`, `SendReceiptReady`, or `sendReceiptReady`
   takes its strategy as a *parameter* instead of constructing one
   internally. It never writes `notifier = ConsoleNotifier()` or its
   equivalent. Passing a dependency in from outside like this
   is called **Dependency Injection**. No framework is required; it's
   just "the caller decides which implementation to use, by passing it
   in as an argument." Write one sentence explaining what this function
   could no longer do if it constructed its own `ConsoleNotifier`
   instead of receiving one.
4. Two more names are useful here. A **Factory** centralizes the
   decision of *which* object to create or return, so
   callers don't each have to make that decision themselves. In
   general, a Factory can construct or supply any kind of object; in
   *this* exercise, that decision happens to be which Strategy
   implementation to use. Imagine a function `build_notifier(config)`
   that returns a `ConsoleNotifier` or an `InMemoryNotifier` depending
   on a setting. You haven't built one, but you can now recognize what
   it would look like. Picking a Strategy is simply this exercise's use
   of a Factory, not what a Factory inherently is. An **Adapter** wraps
   something with an incompatible interface so it matches the one your
   code expects. Imagine a third-party SMS library whose method is
   called `sendMessage(text)` instead of `send(message)`. A small
   wrapper translating one call into the other is an Adapter. Write one
   sentence per pattern in your notes, in your own words.
5. For practice, add one more discount code to Version B in **your
   chosen track**. `"SAVE_FLAT2"` takes $2 off and needs its own test.
   Note how many lines you added and whether any existing logic had to
   change.

### Python

Add `"SAVE_FLAT2": lambda amount: 2.0` to `DISCOUNT_CODES` in
`examples/discount-codes/version-b/python/billing/discount_codes.py`.
Add a test to `tests/test_discount_codes.py` asserting
`apply_discount_code(100.0, "SAVE_FLAT2") == 2.0`.

### Go

Add `"SAVE_FLAT2": func(amount float64) float64 { return 2.0 }` to the
`discountCodes` map in
`examples/discount-codes/version-b/go/billing/discount_codes.go`. Add
a test to `discount_codes_test.go` asserting `ApplyDiscountCode(100.0,
"SAVE_FLAT2")` returns `2.0`. Run `gofmt -w .` afterward. Adding a
longer key to the map shifts how `gofmt` aligns the `:` column for
every entry, and that's expected, not a sign you broke something.

### Java

Add `"SAVE_FLAT2", amount -> 2.0` to the `CODES` map in
`examples/discount-codes/version-b/java/src/main/java/billing/DiscountCodes.java`.
Add a test to `DiscountCodesTest.java` asserting
`DiscountCodes.apply(100.0, "SAVE_FLAT2") == 2.0`.

Don't add this code to Version A or the public starter. It is only for
your own working copy.

## Acceptance criteria

- `my-notes.md` answers points 1, 3, and 4 in your own words (not
  copy-pasted from this README).
- Version B in your chosen track has a new discount code with a passing
  test, and your notes state how many lines and files it took.

## Verification

### Python

```bash
test -f labs/15-patterns-without-worship/my-notes.md && echo "notes exist"
cd examples/discount-codes/version-b/python && uv run pytest -v && cd - > /dev/null
```

The notes should exist, and all 9 tests should pass. Version B started
with 7 tests, and you added one for `SAVE20` in Lab 12 and one here.

### Go

```bash
test -f labs/15-patterns-without-worship/my-notes.md && echo "notes exist"
cd examples/discount-codes/version-b/go && go test ./... -v && cd - > /dev/null
```

The notes should exist, and all 9 tests should pass. Version B started
with 7 tests, and you added one for `SAVE20` in Lab 12 and one here.

### Java

```bash
test -f labs/15-patterns-without-worship/my-notes.md && echo "notes exist"
cd examples/discount-codes/version-b/java && ./gradlew test && cd - > /dev/null
```

The notes should exist, and all 9 tests should pass. Version B started
with 7 tests, and you added one for `SAVE20` in Lab 12 and one here.

## Think about it

- Strategy, Factory, Adapter, and Dependency Injection are four
  different names. Which of them describes *what a piece of code is*
  (a shape), and which describes *how a piece of code receives
  something* (a relationship)? Is your discount-code map or dictionary
  closer to one or the other?
- Now that you have these names, would you have reached for "Strategy"
  as a solution on day one of Lab 12, or was seeing the coupled
  version first (and feeling its cost) necessary to appreciate what the
  pattern actually buys you?

## If you get stuck

### Python

- **Hint 1.** If you're not sure whether something "is a Strategy," ask
  whether you could swap it for a different implementation of the same
  contract without changing the code that calls it. If
  yes, that's the pattern.
- **Hint 2.** Dependency Injection here is not a framework. It simply
  means that the caller chooses an implementation and passes it as an
  argument.
- **Hint 3.** For the new discount code, follow the same structure as
  `"SAVE5"` in `DISCOUNT_CODES`: a lambda that ignores its argument
  and returns a flat amount.

### Go

- **Hint 1.** If you're not sure whether something "is a Strategy," ask
  whether you could swap it for a different implementation of the same
  contract without changing the code that calls it. If
  yes, that's the pattern.
- **Hint 2.** Dependency Injection here is not a framework. It simply
  means that the caller chooses an implementation and passes it as an
  argument.
- **Hint 3.** For the new discount code, follow the same structure as
  `"SAVE5"` in the `discountCodes` map: a function that ignores its
  argument and returns a flat amount.

### Java

- **Hint 1.** If you're not sure whether something "is a Strategy," ask
  whether you could swap it for a different implementation of the same
  contract without changing the code that calls it. If
  yes, that's the pattern.
- **Hint 2.** Dependency Injection here is not a framework. It simply
  means that the caller chooses an implementation and passes it as an
  argument.
- **Hint 3.** For the new discount code, follow the same structure as
  `"SAVE5"` in the `CODES` map: a lambda that ignores its argument and
  returns a flat amount.

## Before you move on to Act IV

Act IV (starting at Lab 16) assumes your `main` branch is clean and
everything from Labs 06-15 is committed and pushed. Check it now.

```bash
git status
```

If this shows anything uncommitted, commit and push it now
(`git add -A && git commit -m "..."; git push`). If it shows clean,
you're ready.

## What's next

You've built a small feature, given it a name a real engineering team
would recognize, and reused it under new requirements without dread.
Act III is done. Next, you stop working alone. "My code works on my
machine" needs to become "my code works when someone else touches it."

Continue to [Lab 16 — Branches exist because work happens in parallel](../16-parallel-branches/README.md).
