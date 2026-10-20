# Lab 10 — One obvious way to check the project

## Story

A new contributor asks, "How do I run the tests again? And was it
the formatter or the analyzer first?" You've typed these commands so
many times that you no longer think about the sequence. A newcomer
should not have to memorize it.

## Learning objectives

After this lab you should be able to:

- Wrap a sequence of commands in a small, readable shell script.
- Explain why a project-level script is preferable to a README
  instruction the reader has to copy by hand.
- Explain what "no hidden magic" means for automation you write
  yourself.

## Before you start

- Lab 09 complete in your chosen track. The formatter, analyzer, and
  tests all succeed individually.
- **A note for Windows:** this course runs on Git Bash on Windows, not
  WSL and not Docker. Git doesn't store a Unix executable bit the same
  way Linux and macOS do, so a script that works when you type
  `./scripts/check.sh` on your machine might not be directly executable
  on a teammate's. The portable way to invoke any of these scripts, on
  every system this course supports, is:
  ```bash
  bash scripts/check.sh
  ```
  That always works, because it never relies on the file's own
  executable bit. You are explicitly telling `bash` to run the file.
  Still mark the scripts executable with `chmod +x scripts/*.sh` below.
  Direct execution is a convenient shortcut on systems that support it.

### Python

- Work from `examples/restaurant-bill/python/`.

### Go

- Work from `examples/restaurant-bill/go/`.

### Java

- Work from `examples/restaurant-bill/java/`. Your scripts use the
  committed `./gradlew`, never a globally installed `gradle`. The
  project does not assume a global installation exists.

## Your task

Create a `scripts/` directory with four scripts, each runnable from
anywhere (they `cd` to the project root themselves, so your current
directory when you invoke them doesn't matter):

1. `scripts/test.sh` runs the test suite.
2. `scripts/check.sh` runs the formatter check and the static
   analyzer (in that order), then the test suite.
3. `scripts/format.sh` reformats the code (not just a
   check).
4. `scripts/run.sh` runs the application.

Make all four executable (`chmod +x scripts/*.sh`). Each script should
be short enough that reading it, top to bottom, tells you exactly what
it does. No separate documentation should be required.

### Python

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(dirname "$0")/.."

uv run pytest
```

That's `scripts/test.sh` in full. `scripts/check.sh` runs
`uv run ruff format --check .`, then `uv run ruff check .`, then
`uv run pytest`. `scripts/format.sh` runs `uv run ruff format .`.
`scripts/run.sh` runs `uv run python main.py`.

### Go

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(dirname "$0")/.."

go test ./...
```

That's `scripts/test.sh` in full. `scripts/format.sh` runs
`gofmt -w .`. `scripts/run.sh` runs `go run main.go`. `scripts/check.sh`
is the one script that needs more than a single command, because of
the `gofmt -l` quirk from Lab 09 (it lists problems but never fails on
its own):

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(dirname "$0")/.."

echo "== gofmt =="
unformatted="$(gofmt -l .)"
if [ -n "$unformatted" ]; then
  echo "Not formatted:"
  echo "$unformatted"
  exit 1
fi

echo "== go vet =="
go vet ./...

echo "== go test =="
go test ./...
```

### Java

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(dirname "$0")/.."

./gradlew test --console=plain
```

That's `scripts/test.sh` in full. `scripts/format.sh` runs
`./gradlew spotlessApply --console=plain`. `scripts/run.sh` runs
`./gradlew run --console=plain`. `scripts/check.sh` runs all three
Gradle checks in order:

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(dirname "$0")/.."
export PMD_JAVA_DISABLE_AUX_CLASSPATH_WARNINGS=true

echo "== spotless (formatter) =="
./gradlew spotlessCheck --console=plain

echo "== pmd (static analysis) =="
./gradlew pmdMain --console=plain

echo "== tests =="
./gradlew test --console=plain
```

(The exported variable just silences a harmless PMD warning from
Lab 09. It doesn't change what gets checked.)

## Acceptance criteria

- All four scripts exist, are executable, and work when invoked with
  `bash scripts/<name>.sh` from a different starting directory (e.g.
  your home directory).
- `scripts/check.sh` exits with a non-zero status if formatting,
  static analysis, or tests fail. A newcomer should see one clear
  failure instead of a script that silently continues.
- Reading any one script takes less than thirty seconds to understand.

## Verification

### Python

```bash
cd ~
bash /path/to/examples/restaurant-bill/python/scripts/test.sh
bash /path/to/examples/restaurant-bill/python/scripts/check.sh
bash /path/to/examples/restaurant-bill/python/scripts/run.sh
cd -
```

### Go

```bash
cd ~
bash /path/to/examples/restaurant-bill/go/scripts/test.sh
bash /path/to/examples/restaurant-bill/go/scripts/check.sh
bash /path/to/examples/restaurant-bill/go/scripts/run.sh
cd -
```

### Java

```bash
cd ~
bash /path/to/examples/restaurant-bill/java/scripts/test.sh
bash /path/to/examples/restaurant-bill/java/scripts/check.sh
bash /path/to/examples/restaurant-bill/java/scripts/run.sh
cd -
```

(Replace `/path/to/` with your actual repository path.) Expected: all
three complete successfully with no manual `cd` on your part.

## Think about it

- What would happen to `scripts/check.sh` if one of its commands
  failed partway through, and the script didn't stop immediately? What
  line in your script prevents that?
- Is there anything about what these scripts do that isn't visible just
  by reading them? If a teammate asked "what does `check.sh` actually
  run," could you just show them the file?

## If you get stuck

### Python

- **Hint 1:** Start every script with `#!/usr/bin/env bash` and `set
  -euo pipefail`. The second line stops the script immediately on the
  first failing command.
- **Hint 2:** To make a script work regardless of the caller's current
  directory, put `cd "$(dirname "$0")/.."` near the top, right after
  `set -euo pipefail`.
- **Hint 3:** `chmod +x scripts/*.sh` makes all four executable at once.

### Go

- **Hint 1:** The same `set -euo pipefail` habit applies here. Without
  it, `scripts/check.sh` would keep running `go vet` even if `gofmt`
  found something. That would defeat the purpose of the check.
- **Hint 2:** `unformatted="$(gofmt -l .)"` captures the command's
  output into a variable instead of printing it immediately. `[ -n
  "$unformatted" ]` then checks whether that variable is non-empty.
- **Hint 3:** If `scripts/run.sh` can't find `main.go` when called from
  another directory, double check the `cd "$(dirname "$0")/.."` line
  is actually the first thing after `set -euo pipefail`.

### Java

- **Hint 1:** `--console=plain` keeps Gradle's output simple and
  script-friendly. Without it, Gradle's fancier terminal output can
  look strange when piped or logged.
- **Hint 2:** If `scripts/run.sh` seems to hang the first time from a
  fresh checkout, Gradle is probably downloading its distribution,
  just as it did on the first run in Lab 06.
- **Hint 3:** All four scripts call `./gradlew`, never a bare `gradle`
  command. If you used `gradle` in one of them, that is probably the
  problem.

Before moving on: commit and push everything from this lab
(`git add -A && git commit -m "..."; git push`). Act IV (starting at
Lab 16) assumes a clean tree from here on.

## What's next

You have turned a one-file monolith into a small, well-tested project
with consistent checks. Act II is complete in all three tracks. The
remaining course through Act VI also supports them: Go and Java now
continue all the way to the capstone alongside Python.

Continue to [Lab 11 — The client changed their mind](../11-changed-requirements/README.md).
