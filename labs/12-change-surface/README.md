# Lab 12 — Where should this change go?

## Story

Two developers independently built the discount-code feature specified
in Lab 11. Both versions behave identically today, but extending them
will take a different amount of work.

This lab and the next three work the same way in every language. When
the instructions split into tracks, read only the one you chose in
Act II.

## Learning objectives

After this lab you should be able to:

- Identify which files need to change when you add a requirement to a
  given design.
- Explain "coupling" and "cohesion" using a concrete example rather than
  a definition.
- Judge a design by its cost of change, not just by whether it currently
  works.

## Before you start

- Labs 06-11 complete in your chosen track.
- Read both `version-a/<your-language>/` and `version-b/<your-language>/`
  before doing anything else. The exact files are listed below. Check
  that both versions pass their tests and calculate the same totals.

### Python

- `examples/discount-codes/version-a/python/billing/calculator.py` and
  `examples/discount-codes/version-b/python/billing/calculator.py`
  (and version B's `billing/discount_codes.py`).

### Go

- `examples/discount-codes/version-a/go/billing/calculator.go` and
  `examples/discount-codes/version-b/go/billing/calculator.go` (and
  version B's `billing/discount_codes.go`).

### Java

- `examples/discount-codes/version-a/java/src/main/java/billing/Calculator.java`
  and
  `examples/discount-codes/version-b/java/src/main/java/billing/Calculator.java`
  (and version B's `billing/DiscountCodes.java`).

## Your task

The owner has added a third code, `SAVE20`. It takes 20% off the amount
remaining after the loyalty discount, just like `SAVE10` but with a
different percentage. Add it to **both** versions in your language.

Use the following figures to check the large order with two $30.00
steaks and a 15% tip. The subtotal is `$60.00`, the loyalty discount is
`$6.00`, the `SAVE20` discount is `$10.80`, the total discount is
`$16.80`, and the expected total is `$53.14`.

### Python

1. Add support for `SAVE20` to **Version A**
   (`examples/discount-codes/version-a/python/`). Add a test in
   `tests/test_calculator.py` asserting that for the large order,
   `bill["discount"] == 16.8` and `bill["total"] == 53.14`.
2. Add support for `SAVE20` to **Version B**
   (`examples/discount-codes/version-b/python/`). Add the equivalent
   test there too.

### Go

1. Add a `case "SAVE20":` branch to the `switch` inside
   `CalculateBill` in **Version A**
   (`examples/discount-codes/version-a/go/`), computing 20% of the
   amount after the loyalty discount. Add
   `TestSave20AppliesAfterLoyaltyDiscount` to `calculator_test.go`,
   asserting `bill.Discount == 16.8` and `bill.Total == 53.14` for the
   large order.
2. Add a `"SAVE20"` entry to the `discountCodes` map in
   `discount_codes.go` in **Version B**
   (`examples/discount-codes/version-b/go/`). Add the equivalent test
   to `calculator_test.go` there too.

### Java

1. Add a `case "SAVE20":` branch to the `switch` inside
   `calculateBill` in **Version A**
   (`examples/discount-codes/version-a/java/`), computing 20% of the
   amount after the loyalty discount. Add `save20AppliesAfterLoyaltyDiscount`
   to `CalculatorTest.java`, asserting `bill.discount == 16.8` and
   `bill.total == 53.14` for the large order.
2. Add a `"SAVE20"` entry to the `CODES` map in `DiscountCodes.java` in
   **Version B** (`examples/discount-codes/version-b/java/`). Add the
   equivalent test to `CalculatorTest.java` there too.

## All tracks

3. For each version, note which files you had to change.
   What *other* code sits next to your change in that file? Look for
   code responsible for something unrelated to discount codes.
4. Record your answer in `examples/discount-codes/COMPARISON.md`.
   Imagine that a tax-calculation bug appears right after your change.
   In which version is it easier to rule out the discount-code change
   as the cause simply by looking at *where* you made it? All language
   tracks share this file, so write it once from the perspective of
   your track.

## Acceptance criteria

- Both versions' test suites pass, including your new `SAVE20` tests.
- `COMPARISON.md` names the specific file changed in each version and
  answers the question in step 4.

### Python

- Five tests should pass in Version A and 8 in Version B.

### Go

- Five tests should pass in Version A (4 existing `Test...` functions
  plus your new one) and 8 in Version B (7 existing plus your new one).

### Java

- Five tests should pass in Version A and 8 in Version B.

## Verification

### Python

```bash
cd examples/discount-codes/version-a/python && uv run pytest -v && cd - > /dev/null
cd examples/discount-codes/version-b/python && uv run pytest -v && cd - > /dev/null
test -f examples/discount-codes/COMPARISON.md && echo "comparison notes exist"
```

You should see 5 passing tests in Version A, 8 in Version B, and the
comparison notes file.

### Go

```bash
cd examples/discount-codes/version-a/go && go test ./... -v && cd - > /dev/null
cd examples/discount-codes/version-b/go && go test ./... -v && cd - > /dev/null
test -f examples/discount-codes/COMPARISON.md && echo "comparison notes exist"
```

You should see 5 passing tests in Version A, 8 in Version B, and the
comparison notes file.

### Java

```bash
cd examples/discount-codes/version-a/java && ./gradlew test && cd - > /dev/null
cd examples/discount-codes/version-b/java && ./gradlew test && cd - > /dev/null
test -f examples/discount-codes/COMPARISON.md && echo "comparison notes exist"
```

You should see successful builds with 5 tests in Version A and 8 in
Version B, as well as the comparison notes file.

## Think about it

- Both versions required you to change exactly one file. Does "same
  number of files changed" mean "same cost of change"? What's actually
  different between the two files you touched?
- In Version B, could you add a fourth discount code without reading a
  single line of the calculator file? What does that tell you about
  how coupled the discount-code file is to the rest of the billing
  logic?

## If you get stuck

### Python

- **Hint 1.** In Version A, your change is a new `elif` branch inside
  `calculate_bill`. In Version B, it's a new entry in the
  `DISCOUNT_CODES` dictionary in `discount_codes.py`.
- **Hint 2.** "20% off the amount remaining after the loyalty discount"
  follows the same rule as `SAVE10`, but uses a different rate.
- **Hint 3.** For the large order with a $60 subtotal, $6 loyalty
  discount, and 15% tip, the amount after the loyalty discount is $54.
  The `SAVE20` discount is $10.80, making the total discount $16.80.

### Go

- **Hint 1.** In Version A, your change is a new `case` inside the
  `switch` in `CalculateBill`. In Version B, it's a new entry in the
  `discountCodes` map in `discount_codes.go`.
- **Hint 2.** "20% off the amount remaining after the loyalty discount"
  follows the same rule as `SAVE10`, but uses a different rate. The
  existing `SAVE10` branch or map entry is a useful template.
- **Hint 3.** For the large order with a $60 subtotal, $6 loyalty
  discount, and 15% tip, the amount after the loyalty discount is $54.
  The `SAVE20` discount is $10.80, making the total discount $16.80.

### Java

- **Hint 1.** In Version A, your change is a new `case` inside the
  `switch` in `calculateBill`. In Version B, it's a new entry in the
  `CODES` map in `DiscountCodes.java`.
- **Hint 2.** "20% off the amount remaining after the loyalty discount"
  follows the same rule as `SAVE10`, but uses a different rate. The
  existing `SAVE10` branch or map entry is a useful template.
- **Hint 3.** For the large order with a $60 subtotal, $6 loyalty
  discount, and 15% tip, the amount after the loyalty discount is $54.
  The `SAVE20` discount is $10.80, making the total discount $16.80.

Before moving on, commit and push everything from this lab
(`git add -A && git commit -m "..."; git push`). Nothing later assumes
a clean tree yet, but Act IV (starting at Lab 16) does. Get into the
habit now.

## What's next

You've seen the difference between a design that makes a new
requirement cheap and one that merely makes it possible. Version A is
still tightly coupled, but it also has tests. Next, you'll bring it
closer to Version B without breaking its behavior along the way.

Continue to [Lab 13 — Refactoring with a safety net](../13-refactoring-safety-net/README.md).
