# Lab 05 — "It works on my machine"

## Story

A teammate sends you a small project and says "just run it." On their
machine, it does. On yours, something's missing, or something's a
version too old or too new. The project does not state what it needs,
so neither of you can diagnose the problem without guessing.

This lab's starter lives in `examples/works-on-my-machine/<your
language>/`. From this point on, every persistent course project lives
under `examples/`. You will also work in one of three tracks: Python,
Go, or Java. Choose the language used by your team, or your preferred
language if you work alone, and follow its section below.

## Learning objectives

After this lab you should be able to:

- Explain why "it runs for me" is not evidence that a program is
  correctly packaged or reproducible.
- Use your track's toolchain to declare and enforce project dependencies
  or a required tool version instead of relying on whatever happens to
  be installed.
- Explain what your track's manifest file is responsible for, and what
  happens when a project asks for something the machine running it
  doesn't have.
- Explain, at a high level, what the devcontainer configuration in
  this repository provisions for all three languages, not just yours.

## Before you start

### Python

- Lab 04 complete.
- Run the commands below from `examples/works-on-my-machine/python/`
  unless stated otherwise.
- `uv` installed. If you're in this repository's Codespace/devcontainer,
  it's already set up (see the root [`README.md`](../../README.md)). If
  you don't have it yet, install it with:
  `curl -LsSf https://astral.sh/uv/0.11.21/install.sh | sh` (pinned to
  the same version as the devcontainer, so everyone in this course is
  running the same `uv`).

### Go

- Lab 04 complete.
- Run the commands below from `examples/works-on-my-machine/go/` unless
  stated otherwise.
- Go 1.27.x installed (see the root README's toolchain table). It is
  already available in this repository's Codespace/devcontainer.

### Java

- Lab 04 complete.
- Run the commands below from `examples/works-on-my-machine/java/`
  unless stated otherwise.
- JDK 21 installed (see the root README's toolchain table). No global
  Gradle installation is needed because the starter includes a
  committed Gradle Wrapper.

## Your task

### Python

1. Without installing anything, try: `python3 main.py` (on Windows Git
   Bash, use `python main.py` instead; Windows' official Python
   installer provides a `python` command, not `python3`). Read the
   error. If the shell instead says the command itself isn't found
   (`command not found`), that's the same lesson one level further
   down. This machine has no Python interpreter yet, let alone the
   project's dependency. In either case, continue to step 4.
2. Open `pyproject.toml` and identify which package the project
   depends on.
3. Run `uv sync`. Look at what appeared in this directory afterward.
4. Run `uv run python main.py`. Compare this result with step 1.
5. Run `uv run pytest` and confirm the test suite passes.
6. Back in the repository root, in a new file
   `labs/05-works-on-my-machine/notes/my-observations.txt`, write, in
   your own words, why step 1 failed, what `uv sync` created and why,
   and what would happen to a teammate who only ran
   `python3 main.py` on their own machine without ever running `uv
   sync`.
7. Open `.devcontainer/devcontainer.json` at the repository root and
   find the line that provisions Python. Add one more sentence to your
   notes file explaining what provisions Go and Java in the same file.

### Go

1. Confirm the starter works exactly as committed:
   ```bash
   go test ./...
   go run .
   ```
   Expect `ok` and `It works on my machine!`.
2. In your working copy, open `go.mod` and raise the `go` line one minor
   version above this course's
   current baseline (`go 1.27` becomes `go 1.28`), to simulate a
   project that now asks for a newer Go than a strict teammate's
   machine has.
3. Run `GOTOOLCHAIN=local go build ./...`. Read the error. It's real:
   `GOTOOLCHAIN=local` tells Go "never download a different toolchain,
   only use what's already on this machine." A strict offline or
   locked-down CI machine might use the same setting deliberately.
4. Run the same build again *without* `GOTOOLCHAIN=local`, i.e. with
   Go's actual default (`auto`): `go build ./...`. You'll see one of
   two things, depending on whether Go 1.28 has shipped by the time
   you try this: either it downloads and the build proceeds, or it
   fails with `toolchain not available`. Either way, notice this is a
   *different* failure than in step 3. `auto` tried to help, but it
   cannot download a release that does not exist. `local` did not try.
5. Change `go.mod` back to the committed value, `go 1.27`. Run
   `go build ./...` again (still the default `auto`, no env var). This
   succeeds because `1.27` is a released version that Go can resolve,
   whether or not it is already installed. The key difference is that
   `auto` obtains the toolchain required by the committed `go.mod`,
   while `local` only uses an installed toolchain.
6. Confirm `go test ./...` passes again on the restored, committed
   state. Course Health checks this state.
7. Back in the repository root, in a new file
   `labs/05-works-on-my-machine/notes/my-observations.txt`, write, in
   your own words, what `GOTOOLCHAIN=local` refused to do in step 3,
   why step 4 failed for a different reason, and what
   would happen to a teammate whose machine only has Go 1.27 if this
   project's committed `go.mod` genuinely required 1.28.
8. Open `.devcontainer/devcontainer.json` at the repository root and
   find the line that provisions Go. Add one more sentence to your
   notes file explaining what provisions Python and Java in the same
   file.

### Java

1. Confirm the starter works exactly as committed:
   ```bash
   ./gradlew test build
   ```
   Expect `BUILD SUCCESSFUL`.
2. In your own working copy, open `build.gradle` and find
   `JavaLanguageVersion.of(21)`. Change `21` to `25`.
3. Run `./gradlew build` again. The error explains
   plainly that no installed JDK matches what the project now asks
   for, and that it isn't configured to download one either.
4. Change `25` back to `21`. Run `./gradlew build` once more and
   confirm it's green again.
5. Back in the repository root, in a new file
   `labs/05-works-on-my-machine/notes/my-observations.txt`, write, in
   your own words, what Gradle refused to do in step 3 and why, where
   the required Java version is declared in this
   project (it isn't a comment, and nobody has to remember it by
   hand), and what would happen to a teammate who tried to build this
   project with JDK 17 instead of 21.
6. Open `.devcontainer/devcontainer.json` at the repository root and
   find the line that provisions Java. Add one more sentence to your
   notes file explaining what provisions Python and Go in the same
   file.

## Acceptance criteria

### Python

- `uv run pytest` passes inside `examples/works-on-my-machine/python/`.
- `.venv/` and `uv.lock` exist in that directory (uv created them; do
  not hand-write either).
- `uv.lock`, created by `uv sync` (not shipped with the starter), is
  committed to the repository. A lock file is only useful to a
  teammate if it's checked in.
- `labs/05-works-on-my-machine/notes/my-observations.txt` answers all
  three points from step 6, plus the devcontainer question from step 7.

### Go

- `go test ./...` and `go run .` both pass on the final, restored
  state, with `go.mod` back to `go 1.27`. Course Health checks this.
- `go.mod` is committed unchanged from the starter (`go 1.27`); the
  bump to `1.28` existed only in your own working copy while you ran
  the experiment, never committed.
- `labs/05-works-on-my-machine/notes/my-observations.txt` answers all
  three points from step 7, plus the devcontainer question from step 8.

### Java

- `./gradlew test build` passes on the final, restored state
  with `JavaLanguageVersion.of(21)`. Course Health checks this.
- `build.gradle` is committed unchanged from the starter
  (`JavaLanguageVersion.of(21)`); the change to `25` existed only in
  your own working copy while you ran the experiment, never committed.
- `labs/05-works-on-my-machine/notes/my-observations.txt` answers all
  three points from step 5, plus the devcontainer question from step 6.

## Verification

### Python

```bash
cd examples/works-on-my-machine/python
uv run pytest
test -f uv.lock && echo "lock file exists"
test -d .venv && echo "virtualenv exists"
cd -
test -f labs/05-works-on-my-machine/notes/my-observations.txt && echo "notes exist"
```

### Go

```bash
cd examples/works-on-my-machine/go
cat go.mod
go test ./...
go run .
cd -
test -f labs/05-works-on-my-machine/notes/my-observations.txt && echo "notes exist"
```

Expected: `go.mod` shows `go 1.27`. If it shows `1.28`, restore it
before finishing. `go test ./...` prints
`ok`; `go run .` prints `It works on my machine!`.

### Java

```bash
cd examples/works-on-my-machine/java
grep JavaLanguageVersion build.gradle
./gradlew test build
cd -
test -f labs/05-works-on-my-machine/notes/my-observations.txt && echo "notes exist"
```

Expected: the `grep` line shows `JavaLanguageVersion.of(21)`. If it
shows `25`, restore it before finishing. `./gradlew test build` ends
in `BUILD SUCCESSFUL`.

## Think about it

- Your track's manifest states what the project needs; a lock file (or
  a pinned toolchain version) states exactly which version satisfies
  that, down to the last digit. Why do you need both instead of just
  one?
- If two teammates on different operating systems build this same
  project from the same committed manifest, should they end up with
  compatible results? Why?
- The devcontainer configuration provisions all three languages
  system-wide, but this lab's actual exercise is about a
  project-level requirement (a Python dependency, a Go toolchain
  version, a Java language version) layered on top of that. What's
  the difference between "the language runtime is available on this
  machine" and "this specific project's requirements are
  reproducible"?

## If you get stuck

### Python

- **Hint 1:** The whole lab is three commands: `uv sync`, `uv run
  python main.py`, `uv run pytest`. Everything else is reading and
  writing notes.
- **Hint 2:** If `python3 main.py` (or `python main.py` on Windows)
  "just works" for you without `uv sync`, it's because `cowsay`
  is already installed globally on your machine. That is the trap this
  lab demonstrates. Try it in a fresh Codespace to see the failure.
- **Hint 3:** `uv run <command>` runs `<command>` inside the project's
  own managed environment, without you needing to manually activate
  anything.

### Go

- **Hint 1:** If step 3's error doesn't mention `1.28` at all, confirm
  you saved `go.mod` after editing it. `go build` reads the file again
  each time and does not cache the old requirement.
- **Hint 2:** `GOTOOLCHAIN` is a real environment variable Go itself
  reads, not something invented for this lab. `go env GOTOOLCHAIN` shows
  your machine's actual default (normally `auto`) when you don't
  override it on the command line.
- **Hint 3:** If you forget whether you restored `go.mod`, `cat
  go.mod` tells you immediately. It should say `go 1.27`, matching
  what `git diff go.mod` would show as no change at all.

### Java

- **Hint 1:** If `./gradlew build` seems to hang or do nothing after
  you change the Java version, give it a few seconds. Gradle is
  checking your installed JDKs before it can tell you none of
  them match.
- **Hint 2:** The error names the exact requirement it couldn't
  satisfy (`languageVersion=25`). That is the
  `JavaLanguageVersion.of(...)` line talking back to you, not a
  generic Gradle failure message.
- **Hint 3:** If you forget whether you restored `build.gradle`, `git
  diff build.gradle` tells you immediately. It should show no
  changes at all.

Before moving on, commit and push everything from this lab with
`git add -A && git commit -m "..."; git push`. In the Python track,
include the generated `uv.lock`. The Go and Java manifests should remain
unchanged, and those tracks have no generated lock file to add. The next
labs do not require a clean tree, but Act IV, starting at Lab 16, does.

## What's next

You now have a small, reproducible project in your chosen language.
Projects rarely remain in one file for long. In the next lab, you will
reorganize a script that has outgrown that simple form.

Continue to [Lab 06 — From script to project](../06-from-script-to-project/README.md).
