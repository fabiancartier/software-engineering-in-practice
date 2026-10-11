# Lab 11 — The client changed their mind

## Story

The restaurant owner says, "We want servers to be able to enter a
discount code at checkout." That's the whole request. It doesn't say
which codes exist, what they're worth, whether they stack with the
existing loyalty discount, or what should happen when someone mistypes
one.

## Learning objectives

After this lab you should be able to:

- Turn a vague request into a list of concrete clarifying questions.
- Turn an agreed requirement into specific input/output examples.
- Explain why "what happens on invalid input" is itself a requirement,
  not an implementation detail.

## Before you start

- Labs 06-10 complete.
- You won't write code in this lab. It focuses entirely on requirements
  analysis.

## Your task

1. Before reading any further, write down at least five questions you
   would ask the restaurant owner about "discount codes at checkout."
   Put them in `labs/11-changed-requirements/my-clarifying-questions.md`.
   Consider which codes exist, what each is worth, whether they combine
   with the existing loyalty discount, what happens with an unrecognized
   code, and whether an order can use more than one code.
2. Now read the agreed specification below. This is what the owner
   actually meant once someone asked the right questions.

   - There are exactly two discount codes: `SAVE10` and `SAVE5`.
   - `SAVE10` takes 10% off the amount that remains *after* the
     existing loyalty discount has already been applied.
   - `SAVE5` takes a flat $5 off that same remaining amount.
   - At most one discount code may be used per order.
   - An unrecognized code is an error. The system must refuse the order
     rather than silently charging full price.
3. In the same notes file, add a table with at least four concrete
   examples. Include an order using `SAVE10`, one using `SAVE5`, one
   without a code, and one with a code that doesn't exist. For each
   example, give the expected total or error.

## Acceptance criteria

- `my-clarifying-questions.md` contains at least five distinct
  clarifying questions written *before* you read the resolved spec.
- The same file contains an examples table with at least four rows
  covering the scenarios listed in step 3.

## Verification

```bash
test -f labs/11-changed-requirements/my-clarifying-questions.md && echo "notes exist"
grep -cE '^[[:space:]]*([0-9]+\.|[-*])[[:space:]]+\S' labs/11-changed-requirements/my-clarifying-questions.md
```

(The second command counts list items in *any* Markdown list style,
whether numbered, `-`, or `*`. This lab is about asking good questions,
not choosing a particular list syntax.)

There's no automated check for the *content* of a requirements
analysis. Verify this part yourself by rereading your notes and making
sure every example has one unambiguous outcome.

## Think about it

- How many of your five questions does the agreed specification answer?
  What would you do with the unanswered ones in a real project?
- "An unrecognized code is an error" is itself a design decision, not
  an obvious default. What would change about the system's behavior if
  the owner had instead said "just ignore codes we don't recognize"?

## If you get stuck

- **Hint 1.** Good clarifying questions are answerable with a specific
  fact, not "it depends." "Do discount codes expire?" is better than
  "how should discount codes work?"
- **Hint 2.** For the examples table, pick concrete numbers: an actual
  order total, an actual code, and an actual expected outcome. Avoid
  descriptions like "some order."
- **Hint 3.** If you're unsure whether `SAVE10` applies before or after
  the loyalty discount, reread the specification. The order is stated
  explicitly.

Before moving on, commit and push everything from this lab
(`git add -A && git commit -m "..."; git push`). Nothing later assumes
a clean tree yet, but Act IV (starting at Lab 16) does. Get into the
habit now.

## What's next

You know what needs to be built. Now you need to find where this change
belongs in the code.

Continue to [Lab 12 — Where should this change go?](../12-change-surface/README.md).
