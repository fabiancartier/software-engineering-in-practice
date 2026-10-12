# Lab 22 — Code changed, old data remained

## Story

Every restart of `order-api` erases all orders because they exist only
in memory. Now the kitchen wants a `notes` field for requests such as
"extra crispy" or "no onions." You need to change the schema without
losing data that already exists.

## Learning objectives

After this lab you should be able to:

- Replace in-memory state with a persistent SQLite-backed store without
  changing the API's external contract.
- Write a migration that adds a column without damaging old rows or
  crashing when it reads them.
- Isolate test databases so tests never touch real data or carry state
  from one run to the next.
- Explain why "the code still runs" is not the same as "the data is
  still correct."

## Before you start

### Python

- Complete Lab 21. `CONTRACT.md` should exist, item validation should
  work, and `uv run pytest` should pass all 4 tests.
- Work in `examples/order-api/python/`.

### Go

- Complete Lab 21. `CONTRACT.md` should exist, item validation should
  work, and `go test ./...` should pass all 4 tests.
- Work in `examples/order-api/go/`.

### Java

- Complete Lab 21. `CONTRACT.md` should exist, item validation should
  work, and `./gradlew test` should pass all 4 tests.
- Work in `examples/order-api/java/`.

## Your task

**Part 1. Add persistence without changing behavior**

### Step 1. Add a SQLite storage layer

All three tracks use the same minimal schema.

```sql
CREATE TABLE IF NOT EXISTS orders (
    order_id INTEGER PRIMARY KEY AUTOINCREMENT,
    items TEXT NOT NULL,
    status TEXT NOT NULL
)
```

Store `items` as a JSON string. SQLite does not need to know that it
represents a list. Do not add a `notes` column yet; that comes in Part 2.

#### Python

Create `db.py` with the following elements.

- Define `DB_PATH = os.environ.get("ORDER_DB_PATH", "orders.db")` at
  module level.
- `init_db()` creates the `orders` table if it does not exist, using
  `sqlite3` from the standard library.
- `create_order(items: list) -> dict` inserts a row and returns the
  new order as a dict (`order_id` as a string, `status` as
  `"received"`).
- `get_order(order_id: str) -> dict | None` looks up a row by id,
  returning `None` if it doesn't exist.

#### Go

Add the SQLite driver. From `examples/order-api/go/`, run:

```bash
go get modernc.org/sqlite@v1.60.1
```

This driver is written entirely in Go and does not require a C
compiler, a system installation of SQLite, or CGO. It behaves the same
on Windows, Linux, and macOS. The command updates `go.mod` and creates
or updates `go.sum`; commit both files. Downloading the module and its
dependencies may take up to a minute on the first run.

Create `db.go` with the following elements.

- Add the blank import `_ "modernc.org/sqlite"` so the driver registers
  itself.
- `dbPath() string` returns `os.Getenv("ORDER_DB_PATH")` if set,
  otherwise `"orders.db"`.
- `openDB() (*sql.DB, error)` calls `sql.Open("sqlite", dbPath())`
  and stores the result in a package-level `*sql.DB` variable (`*sql.DB`
  manages a connection pool and is safe for concurrent use, so it does
  not need the `sync.Mutex` used by the in-memory map).
- `initDB() error` runs the `CREATE TABLE IF NOT EXISTS` statement
  above via `Exec`.
- `createOrder(items []any) (Order, error)` marshals `items` to JSON,
  inserts a row, and reads the generated id back with
  `result.LastInsertId()`.
- `getOrder(id string) (Order, bool, error)` queries by id and returns
  `false` when no row exists. A non-numeric value such as
  `"does-not-exist"` simply matches no row; SQLite does not report a
  type error.

#### Java

Add the SQLite driver. Open `build.gradle` and add this line to the
existing `dependencies { ... }` block, right after the Gson line:

```groovy
implementation 'org.xerial:sqlite-jdbc:3.53.4.0'
```

This jar includes native SQLite libraries for Windows, Linux, and macOS,
including Apple Silicon. There is nothing else to install. The next
`./gradlew` command downloads it automatically, just as it downloaded
Gson and JUnit in earlier labs.

Create `OrderDb.java` with these static methods.

- `dbPath() -> String` returns `System.getenv("ORDER_DB_PATH")` if
  set, otherwise `"orders.db"`.
- `connect() -> Connection` calls `DriverManager.getConnection("jdbc:sqlite:" +
  dbPath())`.
- `initDb() throws SQLException` runs the `CREATE TABLE IF NOT
  EXISTS` statement above.
- `createOrder(List<Object> items, String notes) -> Order` inserts a
  row with a `PreparedStatement`, using
  `Statement.RETURN_GENERATED_KEYS` to read back the new `order_id`.
  Ignore the `notes` parameter for now because the Part 1 table has no
  such column. You will connect it in Part 2.
- `getOrder(String id) -> Order` queries by id with a
  `PreparedStatement` and returns `null` when no row exists.

Use `try-with-resources` for every `Connection`, `PreparedStatement`,
and `ResultSet` you open.

### Step 2. Connect the HTTP handlers to storage

#### Python

Rewrite `api.py` to call `db.create_order` and `db.get_order` instead of
using the `ORDERS` dict. Call `db.init_db()` once from `run()` when the
server starts.

#### Go

Rewrite `handleCreateOrder` and `handleGetOrder` in `api.go` to call
`createOrder` and `getOrder` instead of using the `orders` map. Delete
the unused package variables `mu sync.Mutex`, `orders map[string]Order`,
and `nextID`; `*sql.DB` replaces all three. In `main()`, call `openDB()`
and then `initDB()` before `http.ListenAndServe`. Print any error and
exit, following the existing handling of the `ListenAndServe` error.

#### Java

Rewrite `handleCreateOrder` and `handleGetOrder` in `ApiServer.java` to
call `OrderDb.createOrder` and `OrderDb.getOrder` instead of using the
`ConcurrentHashMap` and `AtomicInteger` fields. Delete both fields.
Catch `SQLException` around database calls and respond with `500` on
failure. In `Main.java`, call `OrderDb.initDb()` before `server.start()`
and add `throws SQLException` to the `main` signature.

### Step 3. Isolate tests from the real database

Tests must never touch the real `orders.db` or carry state between
runs. All three tracks normally read the database path from the
`ORDER_DB_PATH` environment variable and default to `orders.db`. Each
test should instead use a new file in a temporary directory.

#### Python

In `tests/test_api.py`, use pytest's `tmp_path` and `monkeypatch`
fixtures to set `db.DB_PATH` to a file inside `tmp_path` before
calling `db.init_db()` and starting the server under test.

#### Go

In `api_test.go`, at the top of `newTestServer`, call
`os.Setenv("ORDER_DB_PATH", filepath.Join(t.TempDir(), "test.db"))`
before calling `openDB()` and `initDB()`. Because `dbPath()` reads the
environment variable each time, this points the test to its own
temporary file. Change `newTestServer` to take
`t *testing.T` (it needs it for `t.TempDir()`), and update every
existing call site to pass `t`.

#### Java

A JVM cannot clear an environment variable it has already read. Use a
system property in `ApiServerTest.java` as a test-only override with
priority over `ORDER_DB_PATH`. Change `OrderDb.dbPath()` to
check `System.getProperty("order.db.path")` first, then
`System.getenv("ORDER_DB_PATH")`, then fall back to `"orders.db"`. In
the test class, add a `@TempDir Path tempDir` field, and in
`@BeforeEach` (before `startServer()`), call
`System.setProperty("order.db.path", tempDir.resolve("test.db").toString())`
followed by `OrderDb.initDb()`. In `@AfterEach` (after `stopServer()`),
call `System.clearProperty("order.db.path")`.

### Step 4. Run the full suite

All tests from Lab 21 must still pass. This refactor should preserve
behavior, just like the one in Lab 06.

#### Python

```bash
uv run pytest -v
```

#### Go

```bash
go test ./... -v
```

#### Java

```bash
./gradlew test
```

Expect 4 passing tests, the same as at the end of Lab 21.

**Now prove the "old data" problem is real, before you solve it.**

Do not replace this exercise with a hand-written JSON file that
pretends to be an old record. You need to apply a schema change to a
row created by an earlier version of the code. **Keep the database file
until the end of Part 2.**

### Step 5. Create a real order and stop the server

Start the server in Terminal A.

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

Create one order in Terminal B.

```bash
curl -s -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" -d '{"items": ["Burger"]}'
```

Write down the returned `order_id`. You will need it in the next two
steps and again in Part 2. Stop the server in Terminal A with `Ctrl+C`,
but keep the database file.

### Step 6. Restart and confirm that the order survived

Start the server again with the same command, from the same directory,
and with the same database file. Fetch the new order in Terminal B.
The example uses `1`; replace it with the id returned in Step 5 if
needed.

```bash
curl -s http://localhost:8000/orders/1
```

The response must contain `status: "received"` and the same `items` you
sent. This restart proves that Part 1 works outside the test suite.
Stop the server again and keep the database file.

### Step 7. Keep the database file out of Git

Check the root `.gitignore`. It should already ignore `*.db` and the
SQLite sidecar files `*.db-journal`, `*.db-wal`, and `*.db-shm`. Add
these four patterns if they are missing, for example in an older fork.
Use `git status` to confirm that the database is not listed as an
untracked file. It is runtime state generated by the server, not source
code, and must not be committed.

**Part 2 — schema evolution:**

### Step 8 — write the migration

#### Python

Add `migrate_add_notes_column()` to `db.py`: check `PRAGMA
table_info(orders)` for a column named `notes`, and if it's missing,
run `ALTER TABLE orders ADD COLUMN notes TEXT`. If it's already there,
do nothing — running this twice must be safe.

#### Go

Add `migrateAddNotesColumn() error` to `db.go`: run `PRAGMA
table_info(orders)` via `Query`, scan each row's `name` column to check
whether `"notes"` is already present, and if not, run `ALTER TABLE
orders ADD COLUMN notes TEXT`. If it's already there, do nothing.

#### Java

Add `migrateAddNotesColumn() throws SQLException` to `OrderDb.java`:
run `PRAGMA table_info(orders)`, read each row's `name` column via
`ResultSet.getString("name")` to check whether `"notes"` is already
present, and if not, run `ALTER TABLE orders ADD COLUMN notes TEXT`. If
it's already there, do nothing.

No ORM, no Flyway, no Liquibase, no migration framework — this one
`ALTER TABLE`, guarded by a check, is the entire migration.

### Step 9 — run the migration at startup

#### Python

In `run()`, call `db.migrate_add_notes_column()` right after
`db.init_db()`, so every server start ensures the column exists.

#### Go

In `main()`, call `migrateAddNotesColumn()` right after `initDB()`,
handling an error the same way you handled `initDB()`'s.

#### Java

In `Main.java`, call `OrderDb.migrateAddNotesColumn()` right after
`OrderDb.initDb()`.

### Step 10 — run the migration in your tests too

Your tests start the server directly — they never go through
`main`/`run()` — so if you skip this, the `notes` test you add in
Step 16 runs against a database that was never migrated, and either
fails with a confusing "no such column" error or only passes by
accident.

#### Python

In your `tests/test_api.py` fixture, call
`db.migrate_add_notes_column()` right after `db.init_db()`.

#### Go

In `newTestServer`, call `migrateAddNotesColumn()` right after
`initDB()`.

#### Java

In `ApiServerTest.java`'s `@BeforeEach`, call
`OrderDb.migrateAddNotesColumn()` right after `OrderDb.initDb()`.

### Step 11 — teach your storage layer about `notes`

#### Python

Update `create_order` to accept an optional `notes: str = ""`
parameter, storing and returning it. Update `get_order` to include
`notes` in its result, defaulting to `""` if the stored value is
`NULL` (which it will be for the row you created in Step 5).

#### Go

Give `Order` a `Notes string` field (JSON tag `notes`). Update
`createOrder` to take a `notes string` parameter, storing and
returning it. Update `getOrder` to read the `notes` column with
`sql.NullString`, converting a `NULL`/invalid value to `""` (which it
will be for the row you created in Step 5) before putting it on the
returned `Order`.

#### Java

Give `Order` a `final String notes` field and update its constructor
to take it. Update `createOrder` to take a `String notes` parameter,
storing and returning it. Update `getOrder` to read the `notes` column
with `ResultSet.getString`, defaulting to `""` when the value is `null`
(which it will be for the row you created in Step 5 — `ResultSet
.getString` returns Java `null` for a SQL `NULL`).

### Step 12 — accept `notes` from the request body

#### Python

Update `do_POST` in `api.py` to read an optional `notes` field from the
request body (defaulting to `""`) and pass it through to
`db.create_order`.

#### Go

Update `handleCreateOrder` to read an optional `notes` field from the
decoded body (`body["notes"].(string)`, defaulting to `""` if absent or
not a string) and pass it through to `createOrder`.

#### Java

Update `handleCreateOrder` to read an optional `notes` field from the
parsed body (`data.get("notes")`, defaulting to `""` if absent or not a
`String`) and pass it through to `OrderDb.createOrder`.

### Step 13 — restart, and read the historical order

Restart the server — same database file you've been using since
Step 5. Fetch the order you created back then, by its actual id
(replace `1` below if yours is different):

```bash
curl -s http://localhost:8000/orders/1
```

It must still return successfully, now with `notes` present and equal
to `""` — not missing, not a crash. This is the moment the migration
proves itself against a row it didn't create.

### Step 14 — create and fetch a new order with real notes

With the server still running, create a new order that actually sets
`notes`:

```bash
curl -s -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" \
  -d '{"items": ["Burger"], "notes": "no onions"}'
```

Note this order's id too, then fetch it — the example below uses `2`;
replace it with this new order's actual id (`<` and `>` are special to
Bash, so never paste a placeholder like `<new-order-id>` literally):

```bash
curl -s http://localhost:8000/orders/2
```

Confirm `notes` comes back as `"no onions"`, unchanged. Stop the
server.

### Step 15 — restart once more, and confirm nothing broke

Start the server a final time — same database file. Fetch both orders
again (the historical one from Step 5 and the new one from Step 14):
both must return correctly, with their respective `notes` values
unchanged. This restart also re-runs your migration function against a
database that's already migrated — if it crashed or duplicated the
column, you'd see it here. Stop the server.

### Step 16 — add a test for the new field

#### Python

Add a test to `tests/test_api.py` confirming that `POST /orders` with
a `notes` value, followed by `GET` on the returned id, returns that
same `notes` value.

#### Go

Add a test to `api_test.go` confirming the same thing. Follow the
shape of `TestPostThenGetOrder`.

#### Java

Add a test to `ApiServerTest.java` confirming the same thing. Follow
the shape of `postThenGetOrder`.

### Step 17 — update the contract

Update `CONTRACT.md` from Lab 21: `POST /orders`'s request body now
accepts an optional `notes` field, and every response that returns an
order (success and, where applicable, error) now includes `notes`.
For example:

Request: `{"items": ["Burger"], "notes": "no onions"}`

Response: `{"order_id": "4", "items": ["Burger"], "status": "received",
"notes": "no onions"}` (your own `order_id` will depend on how many
orders you've created in this session — don't assume it's always
`"1"`). If `notes` is omitted from the request, or the order predates
this migration, it comes back as `""` — never missing, never `null`.

### Step 18 — branch, PR, review, merge

Do this lab's work on a branch (for example
`feature/data-outlives-code`), push it, and open a pull request. Merge
only once the CI check from Lab 21 is green — the branch → PR → green
CI → merge loop keeps applying to `order-api` for the rest of Act V.

## Acceptance criteria

- A SQLite-backed storage layer exists (`db.py` for Python, `db.go` for
  Go, `OrderDb.java` for Java) with: schema creation, `notes`
  migration, order creation, and order lookup.
- All of Lab 21's tests still pass, plus a new `notes` test (5 total).
- An order created before the `notes` migration ran is still fetchable
  afterward, with `notes == ""`.
- A freshly created order with a real `notes` value roundtrips through
  `POST` then `GET` unchanged, and survives a server restart.
- Running the migration against an already-migrated database doesn't
  crash and doesn't change existing data.
- `CONTRACT.md` reflects the new optional `notes` field.
- This lab's changes were merged through a pull request with a green
  CI check, not committed directly to `main`.

## Verification

### Python

```bash
cd examples/order-api/python
uv run pytest -v
cd -
```

### Go

```bash
cd examples/order-api/go
go test ./... -v
cd -
```

### Java

```bash
cd examples/order-api/java
./gradlew test
cd -
```

Expected: 5 passed (or the Gradle equivalent) — plus everything you
confirmed by hand in Steps 5, 6, 13, 14, and 15, which no test suite
alone would have caught if you'd skipped the real restarts.

## Think about it

- Your migration used `ALTER TABLE ... ADD COLUMN` with no default
  clause, so existing rows get `NULL`. Why did you have to handle that
  `NULL` in your own code, instead of just fixing it in the database
  once?
- Part 1 (SQLite instead of in-memory state) didn't change
  `CONTRACT.md` at all. Part 2 (`notes`) did. What's the difference
  between these two kinds of change, from a caller's point of view?
- You ran the migration check at every startup, instead of once, by
  hand, the first time you needed it. What would've gone wrong if a
  teammate pulled your change and started the server without ever
  running a separate "migrate" command?

## If you get stuck

### Python

- **Hint 1:** `sqlite3.connect(path)` opens (and creates, if missing) a
  database file. `conn.row_factory = sqlite3.Row` lets you access
  columns by name (`row["items"]`) instead of by index.
- **Hint 2:** `cur.lastrowid` after an `INSERT` gives you the
  auto-generated `order_id` for that row.
- **Hint 3:** `PRAGMA table_info(orders)` returns one row per column,
  each with a `name` field — loop over it to check whether `notes`
  already exists before trying to add it again.

### Go

- **Hint 1:** Register the driver with a blank import —
  `import _ "modernc.org/sqlite"` — then `sql.Open("sqlite", path)`.
  `sql.Open` doesn't actually connect; nothing fails until the first
  real query, so call `db.Ping()` right after opening if you want to
  fail fast on a bad path.
- **Hint 2:** `result, err := conn.Exec(...)` followed by
  `result.LastInsertId()` gives you the auto-generated `order_id` as an
  `int64` — convert it with `strconv.FormatInt(id, 10)`.
- **Hint 3:** `rows, err := conn.Query("PRAGMA table_info(orders)")`
  returns one row per column; `rows.Scan` needs a destination for
  every column `PRAGMA table_info` returns (`cid`, `name`, `type`,
  `notnull`, `dflt_value`, `pk`) even though you only care about
  `name`.
- **Hint 4:** `sql.NullString` has a `.Valid` field — `false` means the
  database value was `NULL`, which is exactly the case you need to
  turn into `""`.

### Java

- **Hint 1:** `DriverManager.getConnection("jdbc:sqlite:" + path)`
  opens (and creates, if missing) a database file. sqlite-jdbc
  registers itself automatically — you don't need `Class.forName(...)`.
- **Hint 2:** `statement.executeUpdate(sql, Statement
  .RETURN_GENERATED_KEYS)` followed by `statement.getGeneratedKeys()`
  gives you a `ResultSet` with the auto-generated `order_id` in its
  first column.
- **Hint 3:** `ResultSet.getString("name")` on each row of `PRAGMA
  table_info(orders)` gives you the column name — loop with
  `while (rs.next())` to check whether `"notes"` already exists.
- **Hint 4:** `ResultSet.getString("notes")` returns Java `null` for a
  SQL `NULL` — no exception, no sentinel value, just `null`. Check for
  it before handing the value to your JSON serializer.

## What's next

Your data survives restarts and schema changes, in whichever track you
followed. The kitchen wants a notification sent to an external
delivery service next — and that service doesn't always answer, in
Python, Go, or Java alike.

Continue to [Lab 23 — The outside world fails](../23-outside-world-fails/README.md).
