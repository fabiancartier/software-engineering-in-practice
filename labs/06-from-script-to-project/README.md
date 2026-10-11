# Lab 06 — From script to project

## Story

`examples/restaurant-bill/` calculates a restaurant bill: subtotal, loyalty
discount, tax, tip, total. It works. It is also one function (or one
`main`, in Go and Java) that does five different jobs at once, with no
clear way to change one part without reviewing the whole program.

You picked a Python, Go, or Java track in Lab 05. Whenever this page
splits, read only the section for that language. All three starters solve
the same problem with the same data and lead to an equivalent project
structure.

## Learning objectives

After this lab you should be able to:

- Split a script's distinct responsibilities into separate modules
  (Python), packages (Go), or classes (Java).
- Explain the difference between a pure calculation function and an
  entry point that handles input/output, in this case printing.
- Describe a project to its toolchain with a manifest (`pyproject.toml`,
  `go.mod`, or `build.gradle`) instead of leaving it as a directory of
  unrelated files.

## Before you start

- Labs 01-04 complete.
- Continue with the Python, Go, or Java track you picked in Lab 05. Each
  one is a complete path through Labs 06-10.
- If you already started this project at its old location
  (`examples/restaurant-bill/bill.py`, with no `python/` subfolder),
  nothing is lost: move whatever you'd already created into
  `examples/restaurant-bill/python/` yourself (for example
  `mkdir -p examples/restaurant-bill/python && git mv examples/restaurant-bill/bill.py examples/restaurant-bill/python/`,
  adjusting for whatever files you personally added), then continue
  from here. Don't run `git reset --hard` or `git clean` to "start
  over." Either command could discard work you have not pushed.

### Python

- Run all commands below from `examples/restaurant-bill/python/`.
- `uv` installed (Lab 05 covers this).
- Read `bill.py` fully before changing anything.

### Go

- Run all commands below from `examples/restaurant-bill/go/`.
- Go 1.27.x installed. Check with `go version`. If it's missing, install
  it from the official instructions at
  [go.dev/doc/install](https://go.dev/doc/install). This course does not
  use a Go version manager. If you completed Lab 05 on the same machine,
  Go is already set up.
- Read `main.go` fully before changing anything.

### Java

- Run all commands below from `examples/restaurant-bill/java/`.
- JDK 21 installed. Check with `java -version` (expect `21` somewhere in
  the output). If it's missing, install Adoptium's Temurin 21 build from
  [adoptium.net](https://adoptium.net/temurin/releases/?version=21). No
  global Gradle installation is needed because this project includes a
  committed Gradle Wrapper (`./gradlew`). If you completed Lab 05 on the
  same machine, Java is already set up.
- Read `src/main/java/Main.java` fully before changing anything.

## Your task

### Python

1. Run `python3 bill.py` (Windows Git Bash: `python bill.py`; Windows'
   official Python installer provides `python`, not `python3`), saving
   its output as a baseline you'll compare against later:
   `python3 bill.py | tee /tmp/bill-before.txt` (`python bill.py | tee
   /tmp/bill-before.txt` on Windows).
2. Identify the distinct responsibilities mixed together in `main()`:
   computing a subtotal, applying a discount, computing tax, computing a
   tip, and printing a receipt.
3. Create `pyproject.toml` for this project. Set the name to
   `restaurant-bill`, use `requires-python = ">=3.13"`, add `pytest`
   as a development dependency, and configure
   `[tool.pytest.ini_options]` with `pythonpath = ["."]` (same pattern as
   Lab 05).
4. Create a `billing/` package (`billing/__init__.py`, empty) with a
   module `billing/calculator.py` containing exactly these five pure
   functions, with these exact names and signatures (the next two labs
   depend on these exact names):
   - `calculate_subtotal(items: list[tuple[str, float, int]]) -> float`.
     It returns the sum of `price * quantity` for every item.
   - `calculate_discount(subtotal: float) -> float`. It returns 10% of
     `subtotal` if `subtotal >= 50`, otherwise `0`.
   - `calculate_tax(amount: float) -> float`. It returns 8% of `amount`.
   - `calculate_tip(amount: float, tip_rate: float) -> float`. It returns
     `amount * tip_rate`.
   - `calculate_bill(items: list[tuple[str, float, int]], tip_rate: float)
     -> dict[str, float]`. It composes the four functions above into a
     dict with keys `subtotal`, `discount`, `tax`, `tip`, `total`. **For
     now, compute `tax` from the full `subtotal`, exactly like the
     original script does**. This refactor must reproduce the existing
     behavior, including its bugs. You are not fixing anything yet.
5. Create `billing/cli.py` with a `main()` that calls `calculate_bill`
   *once* and prints a receipt whose output is byte-for-byte identical
   to the original script's, using only the values from the dict it got
   back. Do not recompute any value separately.
6. Create `main.py` at the project root that imports `main` from
   `billing.cli` and calls it under `if __name__ == "__main__":`.
7. Run your new entry point the same way, saving its output too:
   `uv run python main.py | tee /tmp/bill-after.txt`. Diff the two
   files: `diff /tmp/bill-before.txt /tmp/bill-after.txt`.
8. Once the diff is empty, delete `bill.py`; it has been fully replaced.

### Go

1. Run `go run main.go`, saving its output as a baseline:
   `go run main.go | tee /tmp/bill-before.txt`. Notice this works with
   no `go.mod`. When given an explicit file, `go run` does not need a
   module, just as `python3 bill.py` does not need a project manifest.
2. Identify the distinct responsibilities mixed together in `main()`:
   computing a subtotal, applying a discount, computing tax, computing a
   tip, and printing a receipt.
3. Turn this directory into a module with `go mod init restaurant-bill`.
   This creates `go.mod`, Go's equivalent of `pyproject.toml`, and gives
   the project a module path.
4. Create a `billing` package in a new `billing/` subdirectory, with a
   file `billing/calculator.go` (`package billing`) containing exactly
   these five exported functions and two types (the next two labs
   depend on these exact names):
   - `type Item struct { Name string; Price float64; Qty int }`
   - `type Bill struct { Subtotal, Discount, Tax, Tip, Total float64 }`
   - `func CalculateSubtotal(items []Item) float64`. It returns the sum of
     `Price * float64(Qty)` for every item.
   - `func CalculateDiscount(subtotal float64) float64`. It returns 10% of
     `subtotal` if `subtotal >= 50`, otherwise `0`.
   - `func CalculateTax(amount float64) float64`. It returns 8% of `amount`.
   - `func CalculateTip(amount, tipRate float64) float64`. It returns
     `amount * tipRate`.
   - `func CalculateBill(items []Item, tipRate float64) Bill`. It composes
     the four functions above into a `Bill`. **For now, compute `Tax`
     from the full `subtotal`, exactly like the original program does**.
     Preserve the bug for now; you are not fixing behavior yet.
5. Create `billing/cli.go` (same package) with a function
   `func Run(items []Item, tipRate float64)` that calls `CalculateBill`
   *once* and prints a receipt byte-for-byte identical to the original
   program's, using only the fields on the `Bill` it got back.
6. Rewrite `main.go` at the project root so its entire `func main()` is
   just: build the `items` slice, then call `billing.Run(items, 0.15)`.
   You'll need to import this module's own `billing` package by its
   module path: `import "restaurant-bill/billing"`.
7. Run your new entry point the same way, saving its output too:
   `go run main.go | tee /tmp/bill-after.txt`. Diff the two files:
   `diff /tmp/bill-before.txt /tmp/bill-after.txt`.
8. Once the diff is clean, confirm `go run .` (no file argument, the
   module-aware form) produces the same output. This works now because
   `go.mod` exists.

### Java

1. Run the starter, saving its output as a baseline:
   `./gradlew run --console=plain | tee /tmp/bill-before.txt`. The first
   run downloads the Gradle distribution and dependencies if they
   aren't cached yet. This can take a minute or two. Later runs are
   faster.
2. Identify the distinct responsibilities mixed together in `main()`:
   computing a subtotal, applying a discount, computing tax, computing a
   tip, and printing a receipt.
3. Create a `billing` package: a new file
   `src/main/java/billing/Calculator.java` (`package billing;`)
   containing exactly these two nested types and five static methods
   (the next two labs depend on these exact names):
   - `public static class Item` with public fields `name` (`String`),
     `price` (`double`), `qty` (`int`), and a matching constructor.
   - `public static class Bill` with public fields `subtotal`,
     `discount`, `tax`, `tip`, `total` (all `double`), and a matching
     constructor.
   - `public static double calculateSubtotal(List<Item> items)`. It returns the sum of
     `price * qty` for every item.
   - `public static double calculateDiscount(double subtotal)`. It returns 10% of
     `subtotal` if `subtotal >= 50`, otherwise `0`.
   - `public static double calculateTax(double amount)`. It returns 8%
     of `amount`.
   - `public static double calculateTip(double amount, double tipRate)`.
     It returns `amount * tipRate`.
   - `public static Bill calculateBill(List<Item> items, double tipRate)`.
     It composes the four methods above into a `Bill`. **For now, compute
     `tax` from the full `subtotal`, exactly like the original program
     does**. Preserve the bug for now; you are not fixing behavior yet.
4. Create `src/main/java/billing/Cli.java` (same package) with a method
   `public static void run(List<Calculator.Item> items, double tipRate)`
   that calls `Calculator.calculateBill` *once* and prints a receipt
   byte-for-byte identical to the original program's, using only the
   fields on the `Bill` it got back.
5. Rewrite `src/main/java/Main.java` so its entire `main` method is
   just: build the `items` list, then call `Cli.run(items, 0.15)`.
   You'll need `import billing.Calculator;` and `import billing.Cli;`.
6. Add JUnit 5 to `build.gradle` in preparation for Lab 07. Add a
   `repositories { mavenCentral() }` block and these
   dependencies:
   ```gradle
   dependencies {
       testImplementation platform('org.junit:junit-bom:5.11.0')
       testImplementation 'org.junit.jupiter:junit-jupiter'
       testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
   }
   ```
   Also add a `test { useJUnitPlatform() }` block. No test file exists
   yet, so `./gradlew test` succeeds with zero tests.
7. Run your refactored entry point the same way, saving its output too:
   `./gradlew run --console=plain | tee /tmp/bill-after.txt`. Diff the
   two files (you'll need to drop Gradle's own banner lines from the
   comparison, since those aren't part of the program's output).
   Compare the `Receipt` block onward in each file, or directly diff
   the output of `./gradlew run --console=plain --quiet`.

## Acceptance criteria

### Python

- `examples/restaurant-bill/python/bill.py` no longer exists.
- `uv run python main.py` produces output *identical* to the original
  script's output.
- `billing/calculator.py` defines all five functions with the exact names
  and signatures listed above.

### Go

- `examples/restaurant-bill/go/go.mod` exists, declaring module
  `restaurant-bill`.
- `go run .` produces output *identical* to the original program's
  output.
- `billing/calculator.go` defines the `Item` and `Bill` types and all five
  functions with the exact names and signatures listed above.

### Java

- `examples/restaurant-bill/java/build.gradle` declares the JUnit 5
  dependencies and `useJUnitPlatform()`.
- `./gradlew run` produces output *identical* to the original program's
  output.
- `billing/Calculator.java` defines the `Item` and `Bill` types and all five
  methods with the exact names and signatures listed above.

## Verification

### Python

```bash
cd examples/restaurant-bill/python
uv run python main.py | tee /tmp/bill-after.txt
diff /tmp/bill-before.txt /tmp/bill-after.txt && echo "IDENTICAL"
test -f bill.py && echo "ERROR: bill.py still exists" || echo "bill.py correctly removed"
cd -
```

Expected: `IDENTICAL` and `bill.py correctly removed`.

### Go

```bash
cd examples/restaurant-bill/go
go run . | tee /tmp/bill-after.txt
diff /tmp/bill-before.txt /tmp/bill-after.txt && echo "IDENTICAL"
test -f go.mod && echo "go.mod exists" || echo "ERROR: go.mod missing; run go mod init"
cd -
```

Expected: `IDENTICAL` and `go.mod exists`.

### Java

```bash
cd examples/restaurant-bill/java
./gradlew run --console=plain --quiet | tee /tmp/bill-after.txt
diff /tmp/bill-before.txt /tmp/bill-after.txt && echo "IDENTICAL"
./gradlew test
cd -
```

Expected: `IDENTICAL`, and `./gradlew test` succeeds (even with zero
tests, as expected until Lab 07).

## Think about it

- You just proved your refactor didn't change behavior using a manual
  `diff`. What would you have to redo, by hand, every single time you
  changed one more line, without an automated test?
- The calculation step that figures out tax only needs one number to do
  its job. Why is that a useful property for a function (or method) to
  have?

## If you get stuck

### Python

- **Hint 1:** Five functions in `calculator.py`, one function in
  `cli.py`, one two-line `main.py`. That's the whole structure.
- **Hint 2:** `calculate_bill` should call the other four functions.
  Do not reimplement their logic inline.
- **Hint 3:** If your diff isn't empty, print both files with `cat -A`
  or compare a single line at a time. Floating-point formatting (`.2f`)
  often causes small mismatches.

### Go

- **Hint 1:** Five functions plus two types in `calculator.go`, one
  function in `cli.go`, a two-line `main()`. That's the whole structure.
- **Hint 2:** `CalculateBill` should call the other four functions.
  Do not reimplement their logic inline.
- **Hint 3:** The import path for your own package is
  `"<module name from go.mod>/billing"`. If `go run .` can't find it,
  double-check `go.mod`'s first line matches what you're importing.

### Java

- **Hint 1:** Two nested types plus five static methods in
  `Calculator.java`, one static method in `Cli.java`, a two-line `main`.
  That's the whole structure.
- **Hint 2:** `calculateBill` should call the other four methods. Do not
  reimplement their logic inline.
- **Hint 3:** If `./gradlew run` seems to hang the first time, it's
  downloading the Gradle distribution and dependencies. Let it finish;
  later runs will be faster.

Before moving on, commit and push everything from this lab with
`git add -A && git commit -m "..."; git push`. The next labs do not
require a clean tree, but Act IV, starting at Lab 16, does.

## What's next

Your refactor preserved behavior, but preserved does not mean correct.
Right now, checking either claim requires reading the code. In the next
lab, you will automate that work.

Continue to [Lab 07 — How do we know it works?](../07-automated-tests/README.md).
