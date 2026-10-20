# Lab 21 — An API is a contract

## Story

Another part of the kitchen system needs to create orders and check
their status over the network. Its code may use a different language
and cannot import yours. Both sides need an interface they can agree on
without reading each other's source code.

## Learning objectives

After this lab you should be able to:

- Describe an HTTP endpoint's contract, including its requests,
  responses, status codes, and error format.
- Explain why a contract needs to specify error behavior, not just the
  success path.
- Add a new validation rule to an existing endpoint without changing
  its contract for callers who were already following it correctly.

## Before you start

- Complete Labs 06-20 in your chosen track.
- If you already started this project at its old location
  (`examples/order-api/api.py`, with no `python/` subfolder), nothing
  is lost. Move the files you already created into
  `examples/order-api/python/` yourself (for example
  `mkdir -p examples/order-api/python && git mv examples/order-api/api.py examples/order-api/python/`,
  adjusting for whatever files you personally added), then continue
  from here. Do not run `git reset --hard` or `git clean` to "start
  over." Either command would discard work you have not pushed
  anywhere else.

### Python

- Work in `examples/order-api/python/`.
- Confirm that the starter works with `uv run pytest -v`.

### Go

- Work in `examples/order-api/go/`.
- Confirm that the starter works with `go test ./...`.

### Java

- Work in `examples/order-api/java/`.
- Confirm that the starter works with `./gradlew test`.

## Your task

### Step 1. Run the server and explore it by hand

Keep two terminals open, one for the server and one for `curl`. In VS
Code, open the first terminal normally (**Terminal → New Terminal**),
then click the **split
terminal** icon in that panel (or run **Terminal → New Terminal**
again). Both terminals remain in the same VS Code window.

In **Terminal A**, start the server and leave it running.

#### Python

```bash
uv run python api.py
```

#### Go

```bash
go run .
```

#### Java

```bash
./gradlew run --console=plain
```

The first run downloads the Gradle distribution and dependencies if
they are not cached yet. This can take a minute or two. Later runs are
faster.

**All three tracks** print the same line when they are ready.

```text
order-api listening on http://localhost:8000
```

This line confirms that the server is running. Wait for it before
moving to Terminal B.

In **Terminal B**, leave the server running in Terminal A and exercise
it by hand. These commands are the same for every track because they
only use HTTP.

```bash
curl -i -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" \
  -d '{"items": ["Burger", "Fries"]}'
curl -i http://localhost:8000/orders/1
curl -i http://localhost:8000/orders/999
```

Read each response's status line and body before moving to the next
command.

**If `curl` reports** `curl: (7) Failed to connect to localhost port
8000... Couldn't connect to server`, the server in Terminal A is not
running, has crashed, or is using a different port. Check Terminal A
for an error and wait for the "listening" line before trying again.

**If Terminal A reports that the address is already in use**, another
process is listening on port 8000. It is probably a server you started
earlier and forgot to stop. Find it as follows.

- Look for an earlier terminal tab/pane that's still running a server.
- Or run `lsof -i :8000` (macOS/Linux) or, in Git Bash on Windows,
  check Task Manager for a lingering `python`, `go`, or `java`
  process, and stop that process.
- As a last resort, use the `PORT` environment variable supported by
  every track. For example, run `PORT=8001 uv run python api.py`, or
  add the same variable to the `go run .` or `./gradlew run` command.
  Then use `http://localhost:8001` in your `curl` commands.

Once you're done exploring, stop the server in Terminal A with
`Ctrl+C`.

#### Python

Terminal A will print a `KeyboardInterrupt` traceback. This is expected
and harmless. Python uses it to report that `Ctrl+C` interrupted a
blocking call, and the server has stopped.

#### Go

The process exits without another message. This is normal Go behavior
for a program with no special shutdown handling.

#### Java

The process exits, and Gradle may report that the build was cancelled.
Either way, the server has stopped.

### Step 2. Document the contract

Write `examples/order-api/CONTRACT.md`. For each endpoint, document the
HTTP method and path, the request body when present, every possible
response with its status code and body, and the cause of each error.
This HTTP contract is shared by all three tracks because it does not
depend on the implementation language.

The example below shows the structure for one endpoint. Complete the
rest from what you observed in Step 1 rather than guessing.

```markdown
### `POST /orders`

Creates a new order.

**Request body:** `{"items": [<string>, ...]}`

**Responses:**
- `201 Created` — order created. Body:
  `{"order_id": "<string>", "items": [...], "status": "received"}`
- `400 Bad Request` — `items` is missing, not a list, or an empty
  list. Body: `{"error": "items must be a non-empty list"}`
- `400 Bad Request` — the request body isn't valid JSON. Body:
  `{"error": "invalid JSON"}`
```

Do the same for `GET /orders/{id}` (both the found and not-found
cases) and for an unknown path. Confirm every case against a real
response instead of merely copying the template.

### Step 3. Add a validation rule

The owner adds a requirement. **Each entry in `items` must be a
non-empty string.** Reject numbers, empty strings, and `null` values in
the same way as a missing `items` list.

#### Python

In `api.py`'s `do_POST`, right after the existing "must be a non-empty
list" check and before the order is created, add a check that rejects
the request with `400 {"error": "each item must be a non-empty
string"}` if any entry in `items` isn't a non-empty string.

#### Go

In `api.go`'s `handleCreateOrder`, right after the existing
"must be a non-empty list" check (the `len(items) == 0` check) and
before the order is created, add a loop that rejects the request with
`400 {"error": "each item must be a non-empty string"}` the moment it
finds an entry that isn't a non-empty string. Each entry comes out of
the decoded JSON as `any`, so you'll need a type assertion to check
it's actually a `string` before checking it's non-empty.

#### Java

In `ApiServer.java`'s `handleCreateOrder`, right after the existing
"must be a non-empty list" check (the `items.isEmpty()` check) and
before the order is created, add a loop that rejects the request with
`400 {"error": "each item must be a non-empty string"}` the moment it
finds an entry that isn't a non-empty string. Each entry comes out of
the parsed JSON as `Object`, so you'll need an `instanceof` check
before checking it's non-empty.

### Step 4. Add a test

#### Python

Add a test to `tests/test_api.py` confirming that
`POST /orders` with `{"items": ["Burger", ""]}` (or another invalid
entry) returns `400`.

#### Go

Add a test to `api_test.go` confirming that `POST /orders` with
`{"items": ["Burger", ""]}` returns `400`. Use
`TestPostWithoutItemsReturns400`.

#### Java

Add a test to `ApiServerTest.java` confirming that `POST /orders`
with `{"items": ["Burger", ""]}` returns `400`. Use
`postWithoutItemsReturns400`.

### Step 5. Update the contract

Add the new error case to the list of `POST /orders` responses in
`CONTRACT.md`.

### Step 6. Set up CI

Create `.github/workflows/order-api-ci.yml` (same pattern as
Lab 19's `team-inventory-ci.yml`) triggering on `[push, pull_request]`.

#### Python

```yaml
name: order-api CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7

      - uses: actions/setup-python@v7
        with:
          python-version: "3.13"

      - name: Install uv
        uses: astral-sh/setup-uv@v10.2.0
        with:
          version: "0.11.21"

      - name: Install dependencies
        working-directory: examples/order-api/python
        run: uv sync --locked

      - name: Run tests
        working-directory: examples/order-api/python
        run: uv run pytest
```

#### Go

```yaml
name: order-api CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7

      - uses: actions/setup-go@v7
        with:
          go-version: "1.27"

      - name: Run tests
        working-directory: examples/order-api/go
        run: go test ./...
```

#### Java

```yaml
name: order-api CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7

      - uses: actions/setup-java@v6
        with:
          distribution: temurin
          java-version: "21"

      - uses: gradle/actions/setup-gradle@v6

      - name: Run tests
        working-directory: examples/order-api/java
        run: ./gradlew test
```

This workflow runs only your track, so it does not need to configure
Python, Go, *and* Java for one project. The repository's maintenance
workflow, `.github/workflows/course-health.yml`, configures all three
because it checks the entire course.

### Step 7. Open and merge a pull request

Do this lab's work on a branch (for example `feature/api-contract`),
push it, and open a pull request as you did in Lab 18. Start in GitHub's
web interface and check the target repository and branch before
creating the pull request. Open its **Checks** tab and confirm that the
new `order-api CI` workflow is green. Do not rely only on the existing
`team-inventory CI` workflow from Lab 19. Then merge the pull request
and pull the merged change into your local `main`.

## Acceptance criteria

- `examples/order-api/CONTRACT.md` exists and documents every
  endpoint, every status code it can return, and what triggers each
  one, including the new item-validation error case.
- The new item-validation rule is implemented and has a passing test.
- Your track's test command passes with the original three tests plus
  your new one (4 total).
- `.github/workflows/order-api-ci.yml` exists, triggers on push and
  pull request, and runs your track's tests in
  `examples/order-api/<your-language>`.
- This lab's changes were merged through a pull request with a green
  CI check, not committed directly to `main`.

## Verification

### Python

```bash
cd examples/order-api/python
uv run pytest -v
cd -
test -f examples/order-api/CONTRACT.md && echo "contract documented"
test -f .github/workflows/order-api-ci.yml && echo "CI workflow exists"
```

### Go

```bash
cd examples/order-api/go
go test ./... -v
cd -
test -f examples/order-api/CONTRACT.md && echo "contract documented"
test -f .github/workflows/order-api-ci.yml && echo "CI workflow exists"
```

### Java

```bash
cd examples/order-api/java
./gradlew test
cd -
test -f examples/order-api/CONTRACT.md && echo "contract documented"
test -f .github/workflows/order-api-ci.yml && echo "CI workflow exists"
```

Expect `4 passed` (or the Java/Gradle equivalent), `contract
documented`, and `CI workflow exists`. The pull request used to merge
this lab should also have a green check.

## Think about it

- If you changed the response for a successful `POST` to nest `items`
  inside a new `"order"` key instead of at the top level, would that
  break a client written against your current `CONTRACT.md`? Would
  adding a new, optional field to the response break it?
- Your validation rule for `items` entries is new. Could a client that
  was already sending valid data (non-empty strings) even notice this
  change happened?

## If you get stuck

### Python

- **Hint 1.** `curl -i` shows the response status line and headers, not
  just the body. Use them to confirm status codes by hand.
- **Hint 2.** Put the new validation in `do_POST`, after the existing
  "must be a non-empty list" check and before creating the order.
- **Hint 3.** `all(isinstance(item, str) and item.strip() for item in
  items)` is one way to check every item is a non-empty string.

### Go

- **Hint 1.** `curl -i` shows the response status line and headers, not
  just the body. Use them to confirm status codes by hand.
- **Hint 2.** Put the new validation in `handleCreateOrder`, after the
  existing `len(items) == 0` check and before creating the order.
- **Hint 3.** `item.(string)` is a type assertion. Paired with its
  two-value form (`s, ok := item.(string)`), it tells you both whether
  an entry is a string and, if so, gives it to you to check with
  `strings.TrimSpace(s) == ""`.

### Java

- **Hint 1.** `curl -i` shows the response status line and headers, not
  just the body. Use them to confirm status codes by hand.
- **Hint 2.** Put the new validation in `handleCreateOrder`, after the
  existing `items.isEmpty()` check and before creating the order.
- **Hint 3.** Java's pattern-matching `instanceof` (`if (item
  instanceof String s)`) both checks the type and gives you a usable
  `String` variable in one step. Pair it with `s.trim().isEmpty()`.

Before moving on, commit and push everything from this lab
(`git add -A && git commit -m "..."; git push`). Keep using the process
from Act IV: branch, pull request, green CI, and merge. Every change in
Act V follows it, now for `order-api`.

## What's next

Your API works until you restart it and every order disappears. Next,
the data has to survive in Python, Go, and Java alike.

Continue to [Lab 22 — Code changed, old data remained](../22-data-outlives-code/README.md).
