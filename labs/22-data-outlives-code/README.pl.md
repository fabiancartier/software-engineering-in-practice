# Lab 22 — Kod się zmienił, stare dane zostały

## Sytuacja

Każdy restart `order-api` usuwa wszystkie zamówienia, ponieważ istnieją
tylko w pamięci. Teraz kuchnia potrzebuje pola `notes` na uwagi takie
jak „mocno wysmażone” lub „bez cebuli”. Trzeba zmienić schemat bez
utraty istniejących danych.

## Cele nauki

Po tym labie potrafisz:

- Zastąpić stan w pamięci trwałym magazynem SQLite bez zmiany
  zewnętrznego kontraktu API.
- Napisać migrację dodającą kolumnę bez uszkadzania starszych wierszy
  ani błędów podczas ich odczytu.
- Odizolować bazy testowe, aby testy nie korzystały z prawdziwych danych
  i nie przenosiły stanu między uruchomieniami.
- Wyjaśnić, dlaczego działający kod nie gwarantuje poprawności danych.

## Zanim zaczniesz

### Python

- Ukończ lab 21. `CONTRACT.md` powinien istnieć, walidacja elementów
  powinna działać, a `uv run pytest` przechodzić wszystkie 4 testy.
- Pracuj w `examples/order-api/python/`.

### Go

- Ukończ lab 21. `CONTRACT.md` powinien istnieć, walidacja elementów
  powinna działać, a `go test ./...` przechodzić wszystkie 4 testy.
- Pracuj w `examples/order-api/go/`.

### Java

- Ukończ lab 21. `CONTRACT.md` powinien istnieć, walidacja elementów
  powinna działać, a `./gradlew test` przechodzić wszystkie 4 testy.
- Pracuj w `examples/order-api/java/`.

## Twoje zadanie

**Część 1. Dodaj trwałość danych bez zmiany zachowania**

### Krok 1. Dodaj warstwę przechowywania SQLite

Wszystkie trzy ścieżki korzystają z tego samego minimalnego schematu.

```sql
CREATE TABLE IF NOT EXISTS orders (
    order_id INTEGER PRIMARY KEY AUTOINCREMENT,
    items TEXT NOT NULL,
    status TEXT NOT NULL
)
```

Zapisuj `items` jako tekst zawierający JSON. SQLite nie musi wiedzieć,
że reprezentuje on listę. Nie dodawaj jeszcze kolumny `notes`; pojawi
się w części 2.

#### Python

Utwórz `db.py` z poniższymi elementami.

- Na poziomie modułu zdefiniuj
  `DB_PATH = os.environ.get("ORDER_DB_PATH", "orders.db")`.
- `init_db()` tworzy tabelę `orders`, jeśli jeszcze nie istnieje,
  korzystając z `sqlite3` z biblioteki standardowej.
- `create_order(items: list) -> dict` wstawia wiersz i zwraca nowe
  zamówienie jako słownik (`order_id` jako napis, `status` jako
  `"received"`).
- `get_order(order_id: str) -> dict | None` wyszukuje wiersz po
  identyfikatorze i zwraca `None`, jeśli go nie znajdzie.

#### Go

Dodaj sterownik SQLite. Z `examples/order-api/go/` uruchom:

```bash
go get modernc.org/sqlite@v1.60.1
```

Sterownik jest napisany w całości w Go i nie wymaga kompilatora C,
systemowej instalacji SQLite ani CGO. Działa tak samo w systemach
Windows, Linux i macOS. Polecenie aktualizuje `go.mod` oraz tworzy lub
aktualizuje `go.sum`; zapisz oba pliki w commicie. Pierwsze pobranie
modułu i zależności może potrwać do minuty.

Utwórz `db.go` z poniższymi elementami.

- Dodaj pusty import `_ "modernc.org/sqlite"`, aby sterownik się
  zarejestrował.
- `dbPath() string` zwraca `os.Getenv("ORDER_DB_PATH")`, jeśli zmienna
  jest ustawiona, a w przeciwnym razie `"orders.db"`.
- `openDB() (*sql.DB, error)` wywołuje `sql.Open("sqlite", dbPath())`
  i zapisuje wynik w zmiennej `*sql.DB` na poziomie pakietu (`*sql.DB`
  zarządza pulą połączeń i jest bezpieczny przy współbieżnym użyciu,
  dlatego nie potrzebuje `sync.Mutex` używanego przez mapę w pamięci).
- `initDB() error` wykonuje powyższe `CREATE TABLE IF NOT EXISTS`
  przez `Exec`.
- `createOrder(items []any) (Order, error)` serializuje `items` do
  JSON, wstawia wiersz i odczytuje wygenerowane id przez
  `result.LastInsertId()`.
- `getOrder(id string) (Order, bool, error)` wyszukuje wiersz po
  identyfikatorze i zwraca `false`, jeśli go nie znajdzie. Wartość
  nienumeryczna, taka jak `"does-not-exist"`, po prostu nie pasuje do
  żadnego wiersza; SQLite nie zgłosi błędu typu.

#### Java

Dodaj sterownik SQLite. Otwórz `build.gradle` i dodaj tę linię do
istniejącego bloku `dependencies { ... }`, zaraz po linii z Gson:

```groovy
implementation 'org.xerial:sqlite-jdbc:3.53.4.0'
```

Ten plik jar zawiera natywne biblioteki SQLite dla systemów Windows,
Linux i macOS, w tym komputerów z Apple Silicon. Nie trzeba instalować
niczego więcej. Następne polecenie `./gradlew` pobierze go automatycznie,
tak jak wcześniej Gson i JUnit.

Utwórz `OrderDb.java` z poniższymi metodami statycznymi.

- `dbPath() -> String` zwraca `System.getenv("ORDER_DB_PATH")`,
  jeśli jest ustawiona, inaczej `"orders.db"`.
- `connect() -> Connection` wywołuje
  `DriverManager.getConnection("jdbc:sqlite:" +
  dbPath())`.
- `initDb() throws SQLException` wykonuje powyższe `CREATE TABLE IF
  NOT EXISTS`.
- `createOrder(List<Object> items, String notes) -> Order` wstawia
  wiersz przez `PreparedStatement`, używając
  `Statement.RETURN_GENERATED_KEYS` do odczytania nowego `order_id`.
  Na razie zignoruj parametr `notes`, ponieważ tabela z części 1 nie ma
  takiej kolumny. Obsłużysz go w części 2.
- `getOrder(String id) -> Order` wyszukuje wiersz przez
  `PreparedStatement` i zwraca `null`, jeśli go nie znajdzie.

Używaj `try-with-resources` dla każdego otwartego `Connection`,
`PreparedStatement` i `ResultSet`.

### Krok 2. Połącz obsługę HTTP z magazynem

#### Python

Zmień `api.py`, aby wywoływał `db.create_order` i `db.get_order`
zamiast korzystać ze słownika `ORDERS`. Wywołaj `db.init_db()` raz w
`run()`, podczas uruchamiania serwera.

#### Go

Zmień `handleCreateOrder` i `handleGetOrder` w `api.go`, aby wywoływały
`createOrder` i `getOrder` zamiast korzystać z mapy `orders`. Usuń
nieużywane zmienne pakietu `mu sync.Mutex`, `orders map[string]Order`
oraz `nextID`; `*sql.DB` zastępuje wszystkie trzy. W `main()` wywołaj
`openDB()`, a następnie `initDB()` przed `http.ListenAndServe`. W razie
błędu wypisz go i zakończ program, tak jak robi to już obsługa błędu
`ListenAndServe`.

#### Java

Zmień `handleCreateOrder` i `handleGetOrder` w `ApiServer.java`, aby
wywoływały `OrderDb.createOrder` i `OrderDb.getOrder` zamiast korzystać
z pól `ConcurrentHashMap` oraz `AtomicInteger`. Usuń oba pola. Obsłuż
`SQLException` wokół wywołań bazy i w razie błędu odpowiedz kodem `500`.
W `Main.java` wywołaj `OrderDb.initDb()` przed `server.start()` i dodaj
`throws SQLException` do sygnatury `main`.

### Krok 3. Odizoluj testy od prawdziwej bazy danych

Testy nie mogą korzystać z prawdziwego `orders.db` ani przenosić stanu
między uruchomieniami. Wszystkie trzy ścieżki zwykle odczytują ścieżkę
bazy ze zmiennej środowiskowej `ORDER_DB_PATH`, a przy jej braku używają
`orders.db`. Każdy test powinien zamiast tego otrzymać nowy plik w
katalogu tymczasowym.

#### Python

W `tests/test_api.py` użyj mechanizmów `tmp_path` i `monkeypatch` z
pytesta, aby ustawić `db.DB_PATH` na plik wewnątrz `tmp_path` przed
wywołaniem `db.init_db()` i uruchomieniem testowanego serwera.

#### Go

W `api_test.go`, na początku `newTestServer`, wywołaj
`os.Setenv("ORDER_DB_PATH", filepath.Join(t.TempDir(), "test.db"))`
przed wywołaniem `openDB()` i `initDB()`. Funkcja `dbPath()` za każdym
razem ponownie odczytuje zmienną, więc test użyje własnego pliku
tymczasowego. Zmień `newTestServer`, aby przyjmowało `t *testing.T`,
potrzebne do `t.TempDir()`, i przekaż `t` we wszystkich wywołaniach.

#### Java

JVM nie może wyczyścić odczytanej zmiennej środowiskowej. W
`ApiServerTest.java` użyj właściwości systemowej jako testowego
nadpisania z pierwszeństwem przed `ORDER_DB_PATH`. Zmień
`OrderDb.dbPath()`, aby sprawdzało najpierw
`System.getProperty("order.db.path")`, potem
`System.getenv("ORDER_DB_PATH")`, a na końcu wracało do
`"orders.db"`. W klasie testowej dodaj pole `@TempDir Path tempDir` i w
`@BeforeEach` (przed `startServer()`) wywołaj
`System.setProperty("order.db.path", tempDir.resolve("test.db").toString())`,
a potem `OrderDb.initDb()`. W `@AfterEach` (po `stopServer()`) wywołaj
`System.clearProperty("order.db.path")`.

### Krok 4. Uruchom cały zestaw testów

Wszystkie testy z laba 21 muszą nadal przechodzić. Ten refaktoring ma
zachować zachowanie, tak jak w labie 06.

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

Oczekuj 4 przechodzących testów, tak jak na końcu laba 21.

**Teraz potwierdź istnienie problemu starych danych, zanim go
rozwiążesz.**

Nie zastępuj tego ćwiczenia ręcznie napisanym plikiem JSON udającym
stary wiersz. Trzeba zastosować zmianę schematu do danych utworzonych
przez wcześniejszą wersję kodu. **Zachowaj plik bazy do końca części 2.**

### Krok 5. Utwórz prawdziwe zamówienie i zatrzymaj serwer

Uruchom serwer w Terminalu A.

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

Utwórz jedno zamówienie w Terminalu B.

```bash
curl -s -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" -d '{"items": ["Burger"]}'
```

Zapisz zwrócony `order_id`. Będzie potrzebny w dwóch kolejnych krokach
i ponownie w części 2. Zatrzymaj serwer w Terminalu A przez `Ctrl+C`,
ale zachowaj plik bazy danych.

### Krok 6. Uruchom ponownie i sprawdź, czy zamówienie przetrwało

Uruchom serwer ponownie tym samym poleceniem, w tym samym katalogu i z
tym samym plikiem bazy. W Terminalu B pobierz nowe zamówienie. Przykład
używa `1`; w razie potrzeby zastąp je identyfikatorem zwróconym w kroku
5.

```bash
curl -s http://localhost:8000/orders/1
```

Odpowiedź musi zawierać `status: "received"` i te same `items`, które
zostały wysłane. Ten restart potwierdza działanie części 1 poza zestawem
testów. Ponownie zatrzymaj serwer i zachowaj plik bazy.

### Krok 7. Nie zapisuj pliku bazy w Git

Sprawdź główny `.gitignore`. Powinien już ignorować `*.db` oraz pliki
pomocnicze SQLite `*.db-journal`, `*.db-wal` i `*.db-shm`. Jeśli tych
czterech wzorców brakuje, na przykład w starszym forku, dodaj je.
Polecenie `git status` nie powinno pokazywać bazy jako pliku
nieśledzonego. Jest to stan tworzony podczas działania serwera, a nie
kod źródłowy, więc nie należy zapisywać go w repozytorium.

**Część 2 — ewolucja schematu:**

### Krok 8 — napisz migrację

#### Python

Dodaj `migrate_add_notes_column()` do `db.py`: sprawdź `PRAGMA
table_info(orders)` pod kątem kolumny o nazwie `notes`, i jeśli jej
brakuje, uruchom `ALTER TABLE orders ADD COLUMN notes TEXT`. Jeśli już
istnieje, nic nie rób — uruchomienie tego dwa razy musi być bezpieczne.

#### Go

Dodaj `migrateAddNotesColumn() error` do `db.go`: uruchom `PRAGMA
table_info(orders)` przez `Query`, zeskanuj kolumnę `name` każdego
wiersza, żeby sprawdzić, czy `"notes"` już jest obecna, i jeśli nie,
uruchom `ALTER TABLE orders ADD COLUMN notes TEXT`. Jeśli już istnieje,
nic nie rób.

#### Java

Dodaj `migrateAddNotesColumn() throws SQLException` do
`OrderDb.java`: uruchom `PRAGMA table_info(orders)`, odczytaj kolumnę
`name` każdego wiersza przez `ResultSet.getString("name")`, żeby
sprawdzić, czy `"notes"` już jest obecna, i jeśli nie, uruchom `ALTER
TABLE orders ADD COLUMN notes TEXT`. Jeśli już istnieje, nic nie rób.

Żadnego ORM, żadnego Flyway, żadnego Liquibase, żadnego frameworka do
migracji — ten jeden `ALTER TABLE`, zabezpieczony sprawdzeniem, to cała
migracja.

### Krok 9 — uruchamiaj migrację przy starcie

#### Python

W `run()` wywołaj `db.migrate_add_notes_column()` zaraz po
`db.init_db()`, żeby każdy start serwera zapewniał istnienie kolumny.

#### Go

W `main()` wywołaj `migrateAddNotesColumn()` zaraz po `initDB()`,
obsługując błąd tak samo, jak obsłużyłeś/aś błąd `initDB()`.

#### Java

W `Main.java` wywołaj `OrderDb.migrateAddNotesColumn()` zaraz po
`OrderDb.initDb()`.

### Krok 10 — uruchamiaj migrację też w testach

Twoje testy startują serwer bezpośrednio — nigdy nie przechodzą przez
`main`/`run()` — więc jeśli to pominiesz, test `notes` dodawany w
Kroku 16 działa na bazie, która nigdy nie została zmigrowana, i albo
zawiedzie z mylącym błędem "no such column", albo przejdzie tylko
przypadkiem.

#### Python

W fixture `tests/test_api.py` wywołaj `db.migrate_add_notes_column()`
zaraz po `db.init_db()`.

#### Go

W `newTestServer` wywołaj `migrateAddNotesColumn()` zaraz po
`initDB()`.

#### Java

W `@BeforeEach` w `ApiServerTest.java` wywołaj
`OrderDb.migrateAddNotesColumn()` zaraz po `OrderDb.initDb()`.

### Krok 11 — nauczy swoją warstwę przechowywania o `notes`

#### Python

Zaktualizuj `create_order`, żeby przyjmowało opcjonalny parametr
`notes: str = ""`, przechowując go i zwracając. Zaktualizuj
`get_order`, żeby uwzględniało `notes` w wyniku, domyślnie `""`, jeśli
przechowana wartość to `NULL` (co będzie miało miejsce dla wiersza
utworzonego w Kroku 5).

#### Go

Daj `Order` pole `Notes string` (tag JSON `notes`). Zaktualizuj
`createOrder`, żeby przyjmowało parametr `notes string`, przechowując
go i zwracając. Zaktualizuj `getOrder`, żeby odczytywało kolumnę
`notes` przez `sql.NullString`, konwertując wartość `NULL`/nieprawidłową
na `""` (co będzie miało miejsce dla wiersza utworzonego w Kroku 5),
zanim trafi do zwracanego `Order`.

#### Java

Daj `Order` pole `final String notes` i zaktualizuj jego konstruktor,
żeby je przyjmował. Zaktualizuj `createOrder`, żeby przyjmowało
parametr `String notes`, przechowując go i zwracając. Zaktualizuj
`getOrder`, żeby odczytywało kolumnę `notes` przez
`ResultSet.getString`, domyślnie `""`, jeśli wartość to `null` (co
będzie miało miejsce dla wiersza utworzonego w Kroku 5 —
`ResultSet.getString` zwraca Javowe `null` dla SQL-owego `NULL`).

### Krok 12 — przyjmuj `notes` z ciała żądania

#### Python

Zaktualizuj `do_POST` w `api.py`, żeby odczytywało opcjonalne pole
`notes` z ciała żądania (domyślnie `""`) i przekazywało je dalej do
`db.create_order`.

#### Go

Zaktualizuj `handleCreateOrder`, żeby odczytywało opcjonalne pole
`notes` z zdekodowanego ciała (`body["notes"].(string)`, domyślnie
`""`, jeśli brakuje albo nie jest stringiem) i przekazywało je dalej do
`createOrder`.

#### Java

Zaktualizuj `handleCreateOrder`, żeby odczytywało opcjonalne pole
`notes` z sparsowanego ciała (`data.get("notes")`, domyślnie `""`,
jeśli brakuje albo nie jest `String`) i przekazywało je dalej do
`OrderDb.createOrder`.

### Krok 13 — zrestartuj i odczytaj historyczne zamówienie

Zrestartuj serwer — ten sam plik bazy danych, który używasz od Kroku
5. Pobierz zamówienie, które utworzyłeś/aś wtedy, po jego rzeczywistym
id (zastąp `1` poniżej, jeśli Twoje jest inne):

```bash
curl -s http://localhost:8000/orders/1
```

Musi nadal zwrócić się poprawnie, teraz z `notes` obecnym i równym
`""` — nie brakującym, nie awarią. To jest moment, w którym migracja
dowodzi swojej wartości na wierszu, którego nie utworzyła.

### Krok 14 — utwórz i pobierz nowe zamówienie z prawdziwymi notes

Przy wciąż działającym serwerze utwórz nowe zamówienie, które
rzeczywiście ustawia `notes`:

```bash
curl -s -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" \
  -d '{"items": ["Burger"], "notes": "no onions"}'
```

Zanotuj też id tego zamówienia, a potem je pobierz — poniższy przykład
używa `2`; zastąp go rzeczywistym id tego nowego zamówienia (`<` i `>`
są specjalne dla Basha, więc nigdy nie wklejaj placeholdera jak
`<new-order-id>` dosłownie):

```bash
curl -s http://localhost:8000/orders/2
```

Potwierdź, że `notes` wraca jako `"no onions"`, niezmienione. Zatrzymaj
serwer.

### Krok 15 — zrestartuj jeszcze raz i potwierdź, że nic się nie zepsuło

Uruchom serwer ostatni raz — ten sam plik bazy danych. Pobierz oba
zamówienia jeszcze raz (historyczne z Kroku 5 i nowe z Kroku 14): oba
muszą wrócić poprawnie, z niezmienionymi wartościami `notes`. Ten
restart też ponownie uruchamia Twoją funkcję migracji na bazie, która
już jest zmigrowana — jeśli wywaliłaby się albo zduplikowała kolumnę,
zobaczyłbyś/zobaczyłabyś to tutaj. Zatrzymaj serwer.

### Krok 16 — dodaj test dla nowego pola

#### Python

Dodaj test do `tests/test_api.py` potwierdzający, że `POST /orders` z
wartością `notes`, a potem `GET` na zwróconym id, zwraca tę samą
wartość `notes`.

#### Go

Dodaj test do `api_test.go` potwierdzający to samo. Wzoruj się na
kształcie `TestPostThenGetOrder`.

#### Java

Dodaj test do `ApiServerTest.java` potwierdzający to samo. Wzoruj się
na kształcie `postThenGetOrder`.

### Krok 17 — zaktualizuj kontrakt

Zaktualizuj `CONTRACT.md` z Lab 21: ciało żądania `POST /orders` teraz
akceptuje opcjonalne pole `notes`, a każda odpowiedź zwracająca
zamówienie (sukces i, gdzie dotyczy, błąd) teraz zawiera `notes`. Na
przykład:

Żądanie: `{"items": ["Burger"], "notes": "no onions"}`

Odpowiedź: `{"order_id": "4", "items": ["Burger"], "status":
"received", "notes": "no onions"}` (Twoje własne `order_id` będzie
zależeć od tego, ile zamówień utworzyłeś/aś w tej sesji — nie zakładaj,
że to zawsze `"1"`). Jeśli `notes` jest pominięte w żądaniu, albo
zamówienie jest starsze od tej migracji, wraca jako `""` — nigdy
brakujące, nigdy `null`.

### Krok 18 — branch, PR, review, merge

Zrób pracę z tego labu na gałęzi (na przykład
`feature/data-outlives-code`), wypchnij ją i otwórz pull request.
Zmerguj dopiero, gdy check CI z Lab 21 jest zielony — pętla branch →
PR → zielone CI → merge nadal obowiązuje dla `order-api` przez resztę
Aktu V.

## Kryteria akceptacji

- Istnieje warstwa przechowywania oparta na SQLite (`db.py` dla
  Pythona, `db.go` dla Go, `OrderDb.java` dla Javy) z: tworzeniem
  schematu, migracją `notes`, tworzeniem zamówienia i wyszukiwaniem
  zamówienia.
- Wszystkie testy z Lab 21 nadal przechodzą, plus nowy test `notes` (5
  razem).
- Zamówienie utworzone przed uruchomieniem migracji `notes` jest
  nadal pobieralne po niej, z `notes == ""`.
- Świeżo utworzone zamówienie z prawdziwą wartością `notes` przechodzi
  niezmienione przez `POST` a potem `GET` i przetrwa restart serwera.
- Uruchomienie migracji na już zmigrowanej bazie nie wywala się i nie
  zmienia istniejących danych.
- `CONTRACT.md` odzwierciedla nowe opcjonalne pole `notes`.
- Zmiany z tego labu zostały zmergowane przez pull request z zielonym
  checkiem CI, nie zacommitowane bezpośrednio na `main`.

## Weryfikacja

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

Oczekiwane: 5 testów przechodzi (albo odpowiednik Gradle) — plus
wszystko, co potwierdziłeś/aś ręcznie w Krokach 5, 6, 13, 14 i 15,
czego żaden sam zestaw testów by nie wychwycił, gdybyś pominął/ęła
prawdziwe restarty.

## Zastanów się

- Twoja migracja użyła `ALTER TABLE ... ADD COLUMN` bez klauzuli
  domyślnej, więc istniejące wiersze dostają `NULL`. Dlaczego trzeba
  było obsłużyć ten `NULL` we własnym kodzie, zamiast po prostu
  naprawić to raz w bazie danych?
- Część 1 (SQLite zamiast stanu w pamięci) w ogóle nie zmieniła
  `CONTRACT.md`. Część 2 (`notes`) zmieniła. Jaka jest różnica między
  tymi dwoma rodzajami zmian, z punktu widzenia wywołującego?
- Sprawdzenie migracji uruchamiałeś/aś przy każdym starcie, a nie raz,
  ręcznie, pierwszego razu, kiedy było potrzebne. Co by się zepsuło,
  gdyby kolega/koleżanka z zespołu ściągnął/ęła Twoją zmianę i
  uruchomił/a serwer, nigdy nie wywołując osobnej komendy "migrate"?

## Jeśli utkniesz

### Python

- **Podpowiedź 1:** `sqlite3.connect(path)` otwiera (i tworzy, jeśli
  brakuje) plik bazy danych. `conn.row_factory = sqlite3.Row` pozwala
  Ci uzyskać dostęp do kolumn po nazwie (`row["items"]`) zamiast po
  indeksie.
- **Podpowiedź 2:** `cur.lastrowid` po `INSERT` daje Ci automatycznie
  wygenerowany `order_id` dla tego wiersza.
- **Podpowiedź 3:** `PRAGMA table_info(orders)` zwraca po jednym
  wierszu na kolumnę, każdy z polem `name` — przejdź po nich pętlą,
  żeby sprawdzić, czy `notes` już istnieje, zanim spróbujesz dodać ją
  ponownie.

### Go

- **Podpowiedź 1:** Zarejestruj sterownik importem spod znaku
  podkreślenia — `import _ "modernc.org/sqlite"` — potem
  `sql.Open("sqlite", path)`. `sql.Open` nie łączy się naprawdę; nic
  nie zawiedzie aż do pierwszego prawdziwego zapytania, więc wywołaj
  `db.Ping()` zaraz po otwarciu, jeśli chcesz szybko wykryć błędną
  ścieżkę.
- **Podpowiedź 2:** `result, err := conn.Exec(...)` a potem
  `result.LastInsertId()` dają Ci automatycznie wygenerowany
  `order_id` jako `int64` — skonwertuj go `strconv.FormatInt(id, 10)`.
- **Podpowiedź 3:** `rows, err := conn.Query("PRAGMA table_info(orders)")`
  zwraca po jednym wierszu na kolumnę; `rows.Scan` potrzebuje
  miejsca docelowego dla każdej kolumny, którą zwraca `PRAGMA
  table_info` (`cid`, `name`, `type`, `notnull`, `dflt_value`, `pk`),
  nawet jeśli interesuje Cię tylko `name`.
- **Podpowiedź 4:** `sql.NullString` ma pole `.Valid` — `false` znaczy,
  że wartość w bazie była `NULL`, czyli dokładnie ten przypadek, który
  musisz zamienić na `""`.

### Java

- **Podpowiedź 1:** `DriverManager.getConnection("jdbc:sqlite:" + path)`
  otwiera (i tworzy, jeśli brakuje) plik bazy danych. sqlite-jdbc
  rejestruje się automatycznie — nie potrzebujesz
  `Class.forName(...)`.
- **Podpowiedź 2:** `statement.executeUpdate(sql, Statement
  .RETURN_GENERATED_KEYS)` a potem `statement.getGeneratedKeys()` dają
  Ci `ResultSet` z automatycznie wygenerowanym `order_id` w pierwszej
  kolumnie.
- **Podpowiedź 3:** `ResultSet.getString("name")` na każdym wierszu
  `PRAGMA table_info(orders)` daje Ci nazwę kolumny — użyj pętli
  `while (rs.next())`, żeby sprawdzić, czy `"notes"` już istnieje.
- **Podpowiedź 4:** `ResultSet.getString("notes")` zwraca Javowe
  `null` dla SQL-owego `NULL` — bez wyjątku, bez wartości-sentinela,
  po prostu `null`. Sprawdź to, zanim przekażesz wartość do swojego
  serializatora JSON.

## Co dalej

Twoje dane przetrwają restarty i zmiany schematu, w którymkolwiek
tracku realizowałeś/aś. Dalej kuchnia chce wysłać powiadomienie do
zewnętrznego serwisu dostawy — a ten serwis nie zawsze odpowiada, w
Pythonie, Go i Javie tak samo.

Przejdź do [Lab 23 — Świat zewnętrzny zawodzi](../23-outside-world-fails/README.pl.md).
