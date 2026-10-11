# Lab 07 — How do we know it works?

## Story

After the refactoring, the `billing` code still behaves like the
original monolith. You checked it once by hand with `diff`, but
repeating that check after every change would be tedious. It is time
to automate it.

## Learning objectives

After this lab you should be able to:

- Write a unit test using Arrange-Act-Assert.
- Explain what a "unit" is in "unit test", in the context of this
  project.
- Choose test cases that cover a function's or method's distinct
  behaviors, not only its happy path.
- Run a test suite and interpret the result using your language's test
  tools.

## Before you start

- Lab 06 completed in your chosen language.

### Python

- Current directory: `examples/restaurant-bill/python/`.
- `billing/calculator.py` exists with the five functions from Lab 06.

### Go

- Current directory: `examples/restaurant-bill/go/`.
- `billing/calculator.go` exists with the `Item`/`Bill` types and five
  functions from Lab 06.

### Java

- Current directory: `examples/restaurant-bill/java/`.
- `billing/Calculator.java` exists with the `Item`/`Bill` types and five
  methods from Lab 06, and `build.gradle` has the JUnit 5 dependencies.

## Your task

Write tests for the following six cases. Structure each test as Arrange
(set up inputs), Act (call the function), and Assert (check the
result). Each part may be only one line.

1. Computing a subtotal sums `price * quantity` across multiple items.
2. Computing a discount returns `0` for a subtotal below `50`.
3. Computing a discount returns 10% of the subtotal when it is at or
   above `50`.
4. Computing tax returns 8% of whatever amount it's given.
5. Computing a tip returns the given percentage of whatever amount it's
   given.
6. Computing the whole bill for a **small order without a discount**
   returns `46.74`. Use the same three items as the receipt example:
   burger, fries, and soda with a $38 subtotal.

### Python

Create `tests/test_calculator.py`. The example below covers the first
case. Write the other five yourself in a similar form.

```python
from billing.calculator import calculate_subtotal


def test_calculate_subtotal_sums_multiple_items():
    items = [("Burger", 12.50, 2), ("Fries", 4.00, 2)]

    result = calculate_subtotal(items)

    assert result == 33.00
```

### Go

Create `billing/calculator_test.go` in the same package as
`calculator.go`. This gives the tests direct access to the package's
contents. The example below covers the first case; write the other five
yourself. You can combine cases 2 and 3 in a table-driven test using a
`[]struct{...}` of input and expected-value pairs with `t.Run`.

```go
package billing

import "testing"

func TestCalculateSubtotalSumsMultipleItems(t *testing.T) {
	items := []Item{
		{Name: "Burger", Price: 12.50, Qty: 2},
		{Name: "Fries", Price: 4.00, Qty: 2},
	}

	got := CalculateSubtotal(items)

	want := 33.00
	if got != want {
		t.Errorf("got %.2f, want %.2f", got, want)
	}
}
```

### Java

Create `src/test/java/billing/CalculatorTest.java` in package
`billing`, matching `Calculator.java`. The example below covers the
first case. Write the other five yourself in a similar form.

```java
package billing;

import static org.junit.jupiter.api.Assertions.assertEquals;

import java.util.List;
import org.junit.jupiter.api.Test;

class CalculatorTest {

    @Test
    void calculateSubtotalSumsMultipleItems() {
        List<Calculator.Item> items = List.of(
                new Calculator.Item("Burger", 12.50, 2),
                new Calculator.Item("Fries", 4.00, 2));

        double subtotal = Calculator.calculateSubtotal(items);

        assertEquals(33.00, subtotal);
    }
}
```

## Acceptance criteria

- At least six tests exist, one per case listed above. You may write
  more, for example by separating the two discount cases.
- Every test follows Arrange-Act-Assert. Do not add libraries or
  configuration beyond your language's standard test tool.

### Python

- `uv run pytest -v` passes, with at least one test per function listed
  above.

### Go

- `go test ./...` passes, with at least one test (or subtest, if you
  used table-driven tests) per case listed above.

### Java

- `./gradlew test` passes, with at least one test per case listed
  above.

## Verification

### Python

```bash
cd examples/restaurant-bill/python
uv run pytest -v
cd -
```

Every test should be `PASSED`; none should be `FAILED` or skipped.

### Go

```bash
cd examples/restaurant-bill/go
go test ./... -v
cd -
```

Every test should show `--- PASS`, with no `--- FAIL` results.

### Java

```bash
cd examples/restaurant-bill/java
./gradlew test
cd -
```

The expected result is `BUILD SUCCESSFUL`. Open
`build/reports/tests/test/index.html` in a browser if you want to see
each test listed individually.

## Think about it

- All six of your tests pass. Does that prove the whole-bill
  calculation is correct for *every* order, or only for the specific
  inputs you tried?
- You tested a small order and tested the discount calculation with a
  large value. Did any test calculate the entire bill for an order
  that receives a discount? What bug might that case reveal?

## If you get stuck

### Python

- **Hint 1:** Import what you're testing at the top of the file:
  `from billing.calculator import calculate_subtotal, calculate_discount,
  calculate_tax, calculate_tip, calculate_bill`.
- **Hint 2:** A test is just a function starting with `test_` that
  contains `assert` statements — pytest finds and runs it automatically.
- **Hint 3:** For floating-point results, comparing with `==` after
  rounding to 2 decimal places (as `calculate_bill` already does) is
  reliable enough for this project; you don't need `pytest.approx` here.

### Go

- **Hint 1:** Putting the test file in `package billing` (not
  `package billing_test`) is what lets it call unexported details
  directly if you ever need to — though for this lab, the five
  exported functions are all you need.
- **Hint 2:** A test is a function named `TestXxx(t *testing.T)` in a
  file ending `_test.go` — `go test` finds and runs it automatically.
- **Hint 3:** For a table-driven test, a slice of small structs (name,
  input, expected) looped with `for _, c := range cases { t.Run(c.name,
  func(t *testing.T) { ... }) }` keeps repeated cases short without
  copy-pasting the assertion logic.

### Java

- **Hint 1:** `@Test` on a method with no arguments and a `void` return
  type is what JUnit 5 looks for — `./gradlew test` finds and runs it
  automatically.
- **Hint 2:** `assertEquals(expected, actual)` — expected comes first;
  getting the order backwards doesn't break the test, but makes failure
  messages read confusingly.
- **Hint 3:** For floating-point results, `assertEquals(double, double)`
  compares exactly; that's fine here because every value in this
  project (prices, rates, totals) is already an exact two-decimal
  number by the time it's computed.

Before moving on, commit and push the changes from this lab:
`git add -A && git commit -m "..."; git push`. A clean working tree
will be required from Lab 16 onward, so build the habit now.

## What's next

All your tests pass, yet a customer reports an incorrect bill. In the
next lab, you will see why a green test suite does not always mean the
program is correct.

Continue to [Lab 08 — A bug report arrives](../08-bug-report/README.md).
