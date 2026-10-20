# Lab 09 — Machines can check boring things

## Story

Your last code review spent ten minutes discussing tabs versus
spaces, an unused import, and whether a string should use single or
double quotes. None of that was about whether the code does the right
thing. Tests address behavior, although Lab 08 showed that they only
cover scenarios someone considered. Formatting debates still consume
review time even though a machine can settle them.

The three tracks use different tools in this lab because Python, Go,
and Java draw different boundaries between formatting and static
analysis. Read only the section for your track.

## Learning objectives

After this lab you should be able to:

- Distinguish what a formatter checks from what a static analyzer
  checks, and both from what a test checks.
- Add and configure a dev-only tool in your project's own manifest.
- Run your track's formatter and analyzer, and read their output.

## Before you start

- Lab 08 complete in your chosen track. The tax bug is fixed and the
  full suite passes.

### Python

- Work from `examples/restaurant-bill/python/`.

### Go

- Work from `examples/restaurant-bill/go/`. `gofmt` and
  `go vet` ship with the Go toolchain you already installed in Lab 06
  and require no additional installation.

### Java

- Work from `examples/restaurant-bill/java/`. This track adds two
  Gradle plugins, a formatter and a static analyzer. Gradle downloads
  both from Maven Central the first time you run them, just as it did
  with JUnit 5 in Lab 06.

## Your task

### Python

1. Add `ruff` as a dev dependency in `pyproject.toml` (alongside
   `pytest`), then run `uv sync`.
2. Add a `[tool.ruff]` section to `pyproject.toml` with
   `target-version = "py313"` and `line-length = 100`.
3. Run `uv run ruff format --check .`. This tells you whether your
   files are already formatted the way Ruff would format them, without
   changing anything.
4. Run `uv run ruff check .`. This looks for code issues
   (unused imports, unused variables, and similar), which is a different
   question from formatting.
5. Temporarily add an unused import (for example, `import math`) to the
   top of `billing/calculator.py`. Run `uv run ruff check .` again and
   read the specific rule it reports. Remove the import once you've seen
   the message.
6. Fix anything real that either command reported about your own code
   from Labs 06-08. For example, reorder an import block if
   `ruff check .` reports that it is unsorted.

### Go

1. Run `gofmt -l .`. This *lists* files that aren't formatted the way
   `gofmt` would format them. Its exit code is still `0` even
   when it finds something, so a plain `gofmt -l .` in a script would
   silently pass either way. You'll fix that in Lab 10; for now, know
   that "any output at all" means "something needs formatting."
2. Run `go vet ./...`. This looks for mistakes `gofmt` can't see
   (suspicious `Printf` calls, unreachable code, and similar), which is
   a different question from formatting.
3. Temporarily change one of the `fmt.Printf` format verbs in
   `billing/cli.go` so it doesn't match its argument's type. For
   example, change `"Total: $%.2f\n"` to `"Total: %d\n"` where the
   argument is still a `float64`. Run `go vet ./...` again and read what
   it reports. Change the format string back once you've seen the
   message.
4. Run `go test ./...` to confirm the suite is still green (vet and
   gofmt don't run your tests for you).

### Java

1. Add two plugins to `build.gradle`'s `plugins` block:
   ```gradle
   id 'pmd'
   id 'com.diffplug.spotless' version '8.10.3'
   ```
2. Add a `spotless` block configuring Google's Java formatter:
   ```gradle
   spotless {
       java {
           googleJavaFormat()
       }
   }
   ```
3. Run `./gradlew spotlessCheck`. Google's formatter uses 2-space
   indentation, which probably differs from the style used in Labs
   06-08. Expect the first run to report violations in your files. This
   is normal and is not the same as a test failure.
4. Run `./gradlew spotlessApply` to reformat everything, then
   `./gradlew spotlessCheck` again to confirm it's clean now.
5. Create `config/pmd/ruleset.xml`:
   ```xml
   <?xml version="1.0"?>
   <ruleset name="restaurant-bill"
       xmlns="http://pmd.sourceforge.net/ruleset/2.0.0"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://pmd.sourceforge.net/ruleset/2.0.0 https://pmd.sourceforge.io/ruleset_2_0_0.xsd">
       <description>Minimal ruleset for the restaurant-bill Java track: a couple of
       real-mistake checks, not a full default category (which flags
       System.out.println, the whole point of this CLI).</description>

       <rule ref="category/java/bestpractices.xml/UnusedLocalVariable"/>
       <rule ref="category/java/bestpractices.xml/UnusedPrivateField"/>
       <rule ref="category/java/errorprone.xml/EmptyCatchBlock"/>
   </ruleset>
   ```
   This project intentionally does **not** use PMD's full default rule
   categories. One of them (`SystemPrintln`) would flag every
   `System.out.printf` call in `Cli.java`, which is this program's
   entire job. A small, hand-picked ruleset is appropriate here.
6. Add a `pmd` block to `build.gradle`:
   ```gradle
   pmd {
       toolVersion = '7.28.0'
       ruleSetFiles = files('config/pmd/ruleset.xml')
       ruleSets = []
   }
   ```
7. Run `./gradlew pmdMain`. It should pass because your Lab 06-08 code has no
   unused locals, unused fields, or empty catch blocks.
8. Temporarily add a genuinely unused local variable to `Cli.java` (not
   named `unused...` or `ignored...`; PMD deliberately ignores
   variables named that way, which is itself worth noticing). Run
   `./gradlew pmdMain` again and read what it reports. Remove the
   variable once you've seen the message.
9. Run `./gradlew test` to confirm the suite is still green (Spotless
   and PMD don't run your tests for you).

## Acceptance criteria

### Python

- `pyproject.toml` lists `ruff` as a dev dependency and has a
  `[tool.ruff]` section.
- `uv run ruff format --check .` reports no files needing changes.
- `uv run ruff check .` reports no issues.

### Go

- `gofmt -l .` produces no output.
- `go vet ./...` reports nothing.

### Java

- `build.gradle` configures both the `spotless` and `pmd` blocks shown
  above, and `config/pmd/ruleset.xml` exists.
- `./gradlew spotlessCheck` and `./gradlew pmdMain` both succeed.

In every track, the test suite from Labs 07-08 still passes. None of
these tools changes behavior.

## Verification

### Python

```bash
cd examples/restaurant-bill/python
uv run ruff format --check .
uv run ruff check .
uv run pytest
cd -
```

Expected: both Ruff commands report nothing to fix, and `pytest` still
passes.

### Go

```bash
cd examples/restaurant-bill/go
gofmt -l .
go vet ./...
go test ./...
cd -
```

Expected: `gofmt -l .` prints nothing, `go vet` prints nothing, and
`go test` passes.

### Java

```bash
cd examples/restaurant-bill/java
./gradlew spotlessCheck
./gradlew pmdMain
./gradlew test
cd -
```

Expected: all three succeed.

## Think about it

- Which of the tools you've now used on this project could, in
  principle, tell you your code is "correct"? Which ones can only tell
  you it's "consistent" or "free of obvious mistakes"?
- Why run the formatter and the static analyzer as two separate steps
  instead of one?

## If you get stuck

### Python

- **Hint 1:** `uv add --dev ruff` adds the dependency for you instead of
  hand-editing `pyproject.toml`, if you'd rather not edit TOML by hand.
- **Hint 2:** `ruff format` rewrites files to match its style; `ruff
  format --check` only reports what *would* change, without touching
  anything. Use `--check` first.
- **Hint 3:** If `ruff check .` reports nothing at all on your own code,
  that's a valid outcome, not a sign you did something wrong. It means
  your Lab 06-08 code was already clean.

### Go

- **Hint 1:** `gofmt -l .` only *lists* filenames; it never fails on its
  own. A script that wants to fail needs to check whether that output
  is non-empty itself. You will build that check in Lab 10.
- **Hint 2:** `gofmt -w .` (instead of `-l`) actually rewrites files to
  fix formatting, the same way `ruff format` (without `--check`) does
  for Python.
- **Hint 3:** If `go vet ./...` reports nothing at all on your own code,
  that's a valid outcome. It means your Lab 06-08 code had no vet-level
  issues to begin with.

### Java

- **Hint 1:** If `spotlessApply` seems to do nothing, check you saved
  the files it changed. Some editors cache a file's contents until you
  click back into them.
- **Hint 2:** PMD's warning about "the wrong java version" for
  `auxClasspath` is harmless for this project. PMD is being cautious
  about classpath detection, not a real problem with your code.
- **Hint 3:** If `pmdMain` reports nothing at all on your own code,
  that's a valid outcome. It means your Lab 06-08 code had none of the
  three issues this ruleset checks for.

Before moving on, commit and push everything from this lab with
`git add -A && git commit -m "..."; git push`. The next labs do not
require a clean tree, but Act IV, starting at Lab 16, does.

## What's next

You now have three different kinds of automated feedback: tests,
formatting, and static analysis. Right now you have to remember several
different commands in the right order. Next, you will create one command
that runs them all.

Continue to [Lab 10 — One obvious way to check the project](../10-one-way-to-check/README.md).
