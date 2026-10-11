# Lab 08 — A bug report arrives

## Story

An email arrives. "I ordered $60 of food and got the loyalty discount,
but the tax on my receipt looks too high for the discounted amount."
Your test suite is green. The customer is still right.

## Learning objectives

After this lab you should be able to:

- Turn a bug report into a concrete, failing test before touching any
  implementation code.
- Explain why a failing test is better evidence of understanding a bug
  than a print statement.
- Fix a defect with the smallest possible code change, guided by the
  test going from red to green.

## Before you start

- Lab 07 complete in your chosen track, with at least six passing tests.

### Python

- Work from `examples/restaurant-bill/python/`.

### Go

- Work from `examples/restaurant-bill/go/`.

### Java

- Work from `examples/restaurant-bill/java/`.

## Your task

Each track reproduces and fixes the same bug using the same numbers.

1. Reproduce, by hand or in a scratch shell for your language, what the
   whole-bill calculation returns for an order whose subtotal is $60
   (for example, two $30.00 steaks) with a 15% tip rate. Work out by
   hand what the tax *should* be if it's computed on the discounted
   amount ($60 - 10% = $54; 8% of $54 = $4.32) versus what the code
   currently computes.
2. Add a new test asserting that for that $60 order at a 15% tip rate,
   `tax == 4.32` and `total == 66.42`.
3. Run the test suite and confirm this new test fails (red).
4. Read the failure message. Locate the exact line responsible for
   computing tax inside the whole-bill calculation.
5. Fix it by passing the amount *after* the discount to the tax
   calculation instead of the original subtotal.
6. Run the whole suite again and confirm everything passes (green),
   including every test from Lab 07.

### Python

Name the new test
`test_calculate_bill_applies_tax_after_discount_on_large_order`, in
`tests/test_calculator.py`.

### Go

Name the new test
`TestCalculateBillAppliesTaxAfterDiscountOnLargeOrder`, in
`billing/calculator_test.go`.

### Java

Name the new test
`calculateBillAppliesTaxAfterDiscountOnLargeOrder`, in
`src/test/java/billing/CalculatorTest.java`.

## Acceptance criteria

- A test for the discounted $60 order exists, named so its intent is
  clear, and asserts both the tax and the total.
- The full test suite passes completely, with no fewer tests than
  before.
- The fix changes only how tax is computed inside the whole-bill
  calculation. No other function's or method's behavior changes.

## Verification

### Python

```bash
cd examples/restaurant-bill/python
uv run pytest -v
uv run python -c "from billing.calculator import calculate_bill; print(calculate_bill([('Steak', 30.00, 2)], 0.15))"
cd -
```

Expected: all tests `PASSED`; the printed dict shows `'tax': 4.32,
'total': 66.42`.

### Go

```bash
cd examples/restaurant-bill/go
go test ./... -v
cd -
```

Expected: all tests `--- PASS`, including the new one.

### Java

```bash
cd examples/restaurant-bill/java
./gradlew test
cd -
```

Expected: `BUILD SUCCESSFUL`, all tests passing, including the new one.

## Think about it

- Your Lab 07 tests were all green *before* this fix, and the bug still
  existed. What made this bug invisible to that test suite specifically?
- You fixed the bug in the whole-bill calculation, not in the tax
  calculation itself. Why didn't the tax calculation need to change?

## If you get stuck

### Python

- **Hint 1:** Compute the incorrect tax by hand first. For the $60 order,
  what does `calculate_tax(60.0)` return, versus
  `calculate_tax(60.0 - 6.0)`?
- **Hint 2:** The failing assertion message from pytest shows you the
  actual value your code produced. Compare it to what you expected; the
  gap identifies the incorrect input.
- **Hint 3:** The fix is one changed argument on one line inside
  `calculate_bill`. Do not restructure anything else.

### Go

- **Hint 1:** Compute the incorrect tax by hand first. For the $60 order,
  what does `CalculateTax(60.0)` return, versus
  `CalculateTax(60.0 - 6.0)`?
- **Hint 2:** The failing test's `t.Errorf` output shows you the actual
  value your code produced. Compare it to what you expected; the gap
  identifies the incorrect input.
- **Hint 3:** The fix is one changed argument on one line inside
  `CalculateBill`. Do not restructure anything else.

### Java

- **Hint 1:** Compute the incorrect tax by hand first. For the $60 order,
  what does `Calculator.calculateTax(60.0)` return, versus
  `Calculator.calculateTax(60.0 - 6.0)`?
- **Hint 2:** JUnit 5's failure output shows you the actual value your
  code produced next to the expected one. The gap identifies the
  incorrect input.
- **Hint 3:** The fix is one changed argument on one line inside
  `calculateBill`. Do not restructure anything else.

Before moving on, commit and push everything from this lab with
`git add -A && git commit -m "..."; git push`. The next labs do not
require a clean tree, but Act IV, starting at Lab 16, does.

## What's next

You now have a passing suite and a tested fix. Next, you will add a
different kind of check: whether the code follows the team's agreed
style.

Continue to [Lab 09 — Machines can check boring things](../09-automated-checks/README.md).
