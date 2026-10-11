# Lab 13 — Refactoring with a safety net

## Story

Version A works. It has tests. It also has a growing chain of
conditionals inside its bill calculation that have nothing to do with
subtotals, tax, or tips. You're going to fix the *shape* of the code
without changing what it does. The tests will tell you whether you
succeeded.

## Learning objectives

After this lab you should be able to:

- Make a structural change in small steps, each one verified by tests.
- Explain what "behavior-preserving" means when refactoring.
- Use a passing test suite as evidence that a refactor didn't break
  anything, instead of re-reading the whole function by eye.

## Before you start

- Complete Lab 12 first. Your copy of Version A should support `SAVE10`,
  `SAVE5`, and `SAVE20`, with all tests passing.

### Python

- Work in `examples/discount-codes/version-a/python/`.

### Go

- Work in `examples/discount-codes/version-a/go/`.

### Java

- Work in `examples/discount-codes/version-a/java/`.

## Your task

Refactor Version A so that its discount-code handling looks like
Version B's. The tests should pass after every completed step. All
three tracks follow the same sequence.

```text
tests green
→ add new discount-code component
→ existing tests green
→ route behavior through new component
→ tests green
→ remove old conditional logic
→ tests green
```

### Python

1. Create `billing/discount_codes.py` with a `DISCOUNT_CODES` dict
   mapping `"SAVE10"`, `"SAVE5"`, and `"SAVE20"` to functions of the
   amount they apply to (percentages as lambdas, the flat `$5` as a
   lambda that ignores its argument), and an `apply_discount_code(amount,
   code)` function that looks up the code and raises `ValueError` for
   anything unrecognized, matching Version B exactly.
2. Run the full test suite. It should still pass because you've only
   *added* a file. Nothing in `calculator.py` calls it yet.
3. In `calculator.py`, replace the `if/elif/else` chain inside
   `calculate_bill` with a single call to `apply_discount_code`, only
   when `discount_code is not None`.
4. Run the test suite again immediately. It must still pass. If it
   doesn't, you changed behavior rather than just structure. Fix it
   before doing anything else.
5. Delete the now-unused inline logic, if any remains. Run the tests
   one final time.

### Go

1. Create `billing/discount_codes.go` with a `discountCodes` map
   mapping `"SAVE10"`, `"SAVE5"`, and `"SAVE20"` to functions of the
   amount they apply to, and an `ApplyDiscountCode(amount, code)
   (float64, error)` function that looks up the code and returns an
   error for anything unrecognized, matching Version B exactly.
2. Run the full test suite (`go test ./...`). It should still pass.
   Go allows an unused package-level map or function, and nothing in
   `calculator.go` calls the new code yet.
3. In `calculator.go`, replace the `switch` inside `CalculateBill`
   with a single call to `ApplyDiscountCode`, only when
   `discountCode != ""`.
4. Run the test suite again immediately. It must still pass. If it
   doesn't, you changed behavior rather than just structure. Fix it
   before doing anything else.
5. Delete the now-unused inline logic, if any remains. Check whether
   `calculator.go` still needs its `"fmt"` import. Once the `switch`
   that built an error message with `fmt.Errorf` is gone, that import
   becomes unused, and Go refuses to build with an unused import. Run
   `go build ./...` to catch this before you run the tests again.

### Java

1. Create `src/main/java/billing/DiscountCodes.java` with a `CODES` map
   (`Map<String, DoubleUnaryOperator>`) mapping `"SAVE10"`, `"SAVE5"`,
   and `"SAVE20"` to the amount they apply to, and an
   `apply(double amount, String code)` method that looks up the code
   and throws `IllegalArgumentException` for anything unrecognized,
   matching Version B exactly.
2. Run the full test suite (`./gradlew test`). It should still pass.
   An unused class doesn't stop Java from compiling, and nothing in
   `Calculator.java` calls the new code yet.
3. In `Calculator.java`, replace the `switch` inside `calculateBill`
   with a single call to `DiscountCodes.apply`, only when
   `discountCode != null`.
4. Run the test suite again immediately. It must still pass. If it
   doesn't, you changed behavior rather than just structure. Fix it
   before doing anything else.
5. Delete the now-unused inline logic, if any remains. Run the tests
   one final time.

## Acceptance criteria

- The discount-code file (`discount_codes.py`, `discount_codes.go`, or
  `DiscountCodes.java`) contains the same three codes as Version B.
- The bill calculation no longer contains a conditional chain checking
  discount-code strings directly.
- The test suite passes at every step described above, not just at the
  end.

## Verification

### Python

```bash
cd examples/discount-codes/version-a/python
uv run pytest -v
grep -n "elif discount_code" billing/calculator.py && echo "still coupled — not done" || echo "decoupled"
cd -
```

All tests should pass and the command should print `decoupled`.

### Go

```bash
cd examples/discount-codes/version-a/go
go build ./...
go test ./... -v
grep -n 'case "SAVE' billing/calculator.go && echo "still coupled — not done" || echo "decoupled"
cd -
```

The build and tests should pass, and the command should print
`decoupled`.

### Java

```bash
cd examples/discount-codes/version-a/java
./gradlew test
grep -n "switch (discountCode)" src/main/java/billing/Calculator.java && echo "still coupled — not done" || echo "decoupled"
cd -
```

The build and tests should pass, and the command should print
`decoupled`.

## Think about it

- At which step would the test suite immediately catch a typo? Which
  step could introduce a behavior change that the current tests miss?
- You just turned Version A into something structurally identical to
  Version B. What was the actual *evidence*, at each step, that you
  hadn't changed what the program does?

## If you get stuck

### Python

- **Hint 1.** Steps 1-2 only add code, so the existing code cannot break
  yet. That's deliberate. The regression suite still protects the
  existing behavior; the next step routes it through the new structure.
- **Hint 2.** Step 3 is a one-line replacement of the whole `if
  discount_code == "SAVE10": ... elif ...: ... else: raise ...` block
  with `code_discount = apply_discount_code(after_loyalty,
  discount_code)`.
- **Hint 3.** If a test fails after step 3, compare what
  `apply_discount_code` does for that specific code against what the
  old inline branch did. The discrepancy is usually in exactly one of
  the three codes.

### Go

- **Hint 1.** Steps 1-2 only add code, so the existing code cannot break
  yet. Unlike an unused *local* variable, an unused package-level
  function or map is not a compile error in Go.
- **Hint 2.** Step 3 is a one-line replacement of the whole `switch
  discountCode { case "SAVE10": ... default: return Bill{},
  fmt.Errorf(...) }` block with `codeDiscount, err =
  ApplyDiscountCode(afterLoyalty, discountCode)` (plus the `if err !=
  nil` check).
- **Hint 3.** If `go build ./...` complains about `"fmt" imported and
  not used` after step 3, that's expected. The only thing that used
  to use `fmt` was the error message inside the old `switch`. Remove
  the import, or replace it with just `import "math"` if that's the
  only other thing the file still needs.

### Java

- **Hint 1.** Steps 1-2 only add code, so the existing code cannot break
  yet.
- **Hint 2.** Step 3 is a one-line replacement of the whole `switch
  (discountCode) { case "SAVE10": ... default: throw ... }` block with
  `codeDiscount = DiscountCodes.apply(afterLoyalty, discountCode);`.
- **Hint 3.** If a test fails after step 3, compare what
  `DiscountCodes.apply` does for that specific code against what the
  old inline `case` did. The discrepancy is usually in exactly one of
  the three codes.

Before moving on, commit and push everything from this lab
(`git add -A && git commit -m "..."; git push`). Nothing later assumes
a clean tree yet, but Act IV (starting at Lab 16) does. Get into the
habit now.

## What's next

The last two labs showed that discount codes form a family of behaviors
selected by a key. Next, you'll see the same structure in a completely
different part of the system before learning its usual name.

Continue to [Lab 14 — One contract, three languages](../14-one-contract-three-languages/README.md).
