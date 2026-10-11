# Lab 14 — One contract, three languages

## Story

The kitchen wants a notification when an order is ready. A console
message is enough for now, but email or SMS may follow later. Three
teams built the same small contract, one each in Python, Go, and Java.
The idea is the same, although each language expresses it differently.
You only need to change the implementation in your language.

## Learning objectives

After this lab you should be able to:

- Explain what makes something a "contract," regardless of the syntax
  a language uses to express it.
- Explain how your own language recognizes that something satisfies a
  contract, and describe how the other two differ after reading their
  code. You don't need to run them.
- Add a new implementation of an existing contract in your own
  language, with a passing test.

## Before you start

- Complete Labs 01-05 first. Labs 06-13 are not required for this
  particular exercise.
- You only need the toolchain for **your own language**.

### Python

- Install `uv` and work in `examples/notifier/python/`.

### Go

- Install Go 1.27.x and work in `examples/notifier/go/`.

### Java

- Install JDK 21 and work in `examples/notifier/java/`.

## Your task

### Step 1. Read all three contracts

Read the files below even if you only have your own language's
toolchain installed. You don't need to run source code to inspect it.

- Python: `examples/notifier/python/notifier/notifier.py`
- Go: `examples/notifier/go/notifier.go`
- Java: `examples/notifier/java/Notifier.java`,
  `examples/notifier/java/ConsoleNotifier.java`,
  `examples/notifier/java/InMemoryNotifier.java`,
  `examples/notifier/java/ReceiptService.java`

Look for the structure repeated in all three languages. A type named
`Notifier` describes one method, and at least two implementations are
passed to a function. That function calls the method without knowing
which implementation it received. Python uses a `Protocol`; Go and
Java use interfaces.

### Step 2. Run the checks for your track

### Python

```bash
cd examples/notifier/python && uv run pytest -v && cd - > /dev/null
```

### Go

```bash
cd examples/notifier/go && go test ./... && cd - > /dev/null
```

### Java

```bash
cd examples/notifier/java && javac *.java -d out && java -cp out NotifierCheck && cd - > /dev/null
```

### Step 3. Add `SilentNotifier` in your track

### Python

In `notifier/notifier.py`, add a class `SilentNotifier` with a `send`
method that does nothing at all. You don't need `class
SilentNotifier(Notifier)` inheritance because this is duck typing. Add
a test in `tests/test_notifier.py` confirming
`send_receipt_ready(SilentNotifier(), "A123")` runs without raising an
exception.

### Go

In `notifier.go`, add a `type SilentNotifier struct{}` with a
`Send(message string)` method with an empty body. Add a test in
`notifier_test.go` confirming `SendReceiptReady(SilentNotifier{},
"A123")` runs without panicking.

### Java

Add a class `SilentNotifier implements Notifier` in a new file
`SilentNotifier.java`, with an empty `send` method body. In
`NotifierCheck.java`, add a second check that
`ReceiptService.sendReceiptReady(new SilentNotifier(), "A123")` runs
without throwing.

### Step 4. Compare how each language recognizes the contract

You've now read all three implementations and built one. The examples
below show what happens when a type does not satisfy the contract.
They were verified for this course, so you can compare the languages
without installing the other two toolchains.

**Java.** A class with a `send(String message)` method that does
*not* write `implements Notifier` fails to compile the moment you try
to pass it where a `Notifier` is expected, even though the method
exists.

```text
TryBad.java:4: error: incompatible types: BadNotifier cannot be converted to Notifier
        ReceiptService.sendReceiptReady(n, "A123");
                                        ^
```

**Go.** A struct with a *misspelled* method name (`Sand` instead of
`Send`) fails to compile the moment you try to pass it where the
`Notifier` interface is expected. Go checks the structural match
without requiring an `implements` keyword.

```text
./notifier.go:20:19: cannot use BadNotifier{} (value of struct type BadNotifier) as Notifier value in argument to SendReceiptReady: BadNotifier does not implement Notifier (missing method Send)
```

**Python.** An object with a *misspelled* method name (`sand` instead
of `send`) compiles and runs with no complaint at all, right up until
the exact line that calls `.send(...)` executes.

```text
Traceback (most recent call last):
  File "notifier_check.py", line 18, in <module>
    send_receipt_ready(BadNotifier(), "A123")
  File "notifier_check.py", line 9, in send_receipt_ready
    notifier.send(f"Order {order_id} is ready.")
AttributeError: 'BadNotifier' object has no attribute 'send'. Did you mean: 'sand'?
```

If that code path never ran, for example because nothing called the
notifier containing the typo, Python would not raise an exception.
Nothing checked the method, so nothing caught the mistake.

### Step 5. Answer in your own words

In a notes file `labs/14-one-contract-three-languages/my-notes.md`,
answer:

1. In your chosen language, how does it recognize that
   `SilentNotifier` satisfies `Notifier`? Point to the exact mechanism
   (an `implements` clause checked by the compiler, a structural match
   checked by the compiler, or nothing checked at all).
2. Using the verified output from Step 4, which language catches a
   misspelled method name *earliest*? Does it happen before the program
   runs or only when the faulty code path executes?
3. If a teammate handed you a class with the right method but it
   doesn't satisfy the contract correctly (missing `implements` in
   Java, a misspelled method in Go or Python), what's the concrete,
   observable difference between how each language's tooling would
   surface that to you?

## Optional exercise

If you happen to already have the other two toolchains installed, you
can run all three verification commands from Step 2 and build
`SilentNotifier` in all three. This is not required; the acceptance
criteria only cover your chosen track.

## Acceptance criteria

- Your chosen language has a working `SilentNotifier` and a passing
  check for it alongside the existing `ConsoleNotifier` and
  `InMemoryNotifier` checks.
- `my-notes.md` answers all three questions from Step 5, in your own
  words, grounded in Step 4's verified output (not invented behavior).

## Verification

### Python

```bash
cd examples/notifier/python && uv run pytest -v && cd - > /dev/null
test -f labs/14-one-contract-three-languages/my-notes.md && echo "notes exist"
```

### Go

```bash
cd examples/notifier/go && go test ./... -v && cd - > /dev/null
test -f labs/14-one-contract-three-languages/my-notes.md && echo "notes exist"
```

### Java

```bash
cd examples/notifier/java && javac *.java -d out && java -cp out NotifierCheck && cd - > /dev/null
test -f labs/14-one-contract-three-languages/my-notes.md && echo "notes exist"
```

The checks for your track, including the new `SilentNotifier` check,
should pass, and the notes file should exist.

## Think about it

- Go's `interface` and Python's `Protocol` both let you satisfy a
  contract just by having the right method, with no explicit
  declaration. Go's compiler actually checks that match the
  moment you pass your type where the interface is expected, while
  Python runs no such check at all in this lab. What would it cost to
  close that gap for Python? A tool exists for this; what would you
  need to run that this lab doesn't?
- Python's `Protocol` alone gives you *documentation* of a contract,
  not *enforcement*. Is that a flaw in Python, or a trade-off? What do
  you imagine Python programmers gain in exchange for that missing
  check?

## If you get stuck

### Python

- **Hint 1.** `SilentNotifier`'s `send` method body is just `pass`.
- **Hint 2.** You don't need `class SilentNotifier(Notifier)`. Nothing
  in this lab enforces the `Protocol`, so a plain `class
  SilentNotifier:` with a matching `send` method is enough.
- **Hint 3.** The whole lab is one command plus a few lines of code.
  `uv run pytest -v`.

### Go

- **Hint 1.** `SilentNotifier`'s `Send` method body is just an empty
  `{}`.
- **Hint 2.** You don't need to write anything declaring that
  `SilentNotifier` implements `Notifier`. Go checks that the moment
  you pass a `SilentNotifier{}` where a `Notifier` is expected.
- **Hint 3.** The whole lab is one command plus a few lines of code.
  `go test ./...`.

### Java

- **Hint 1.** `SilentNotifier`'s `send` method body is just an empty
  `{}`.
- **Hint 2.** Forgetting `implements Notifier` will not stop
  `SilentNotifier` from compiling on its own, but it *will* stop you
  from passing a bare `SilentNotifier` to `sendReceiptReady`, which
  expects a `Notifier`. That's exactly the Step 4 example, reproduced
  with your own class if you remove the clause to try it.
- **Hint 3.** No build tool is needed.
  `javac *.java -d out && java -cp out NotifierCheck` is the only
  command you need.

Before moving on, commit and push everything from this lab
(`git add -A && git commit -m "..."; git push`). Nothing later assumes
a clean tree yet, but Act IV (starting at Lab 16) does. Get into the
habit now.

## What's next

Discount codes (Labs 12-13) and notifiers (this lab) turn out to share
the same structure. The caller selects one of several interchangeable
behaviors instead of relying on a chain of conditionals buried in the
business logic. Next, you'll give that structure a name.

Continue to [Lab 15 — Patterns without pattern worship](../15-patterns-without-worship/README.md).
