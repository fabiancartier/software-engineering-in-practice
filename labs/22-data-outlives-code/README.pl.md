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

**Część 2. Rozwijaj schemat**

### Krok 8. Napisz migrację

#### Python

Dodaj `migrate_add_notes_column()` do `db.py`. Za pomocą `PRAGMA
table_info(orders)` sprawdź, czy istnieje kolumna `notes`. Jeśli jej
brakuje, wykonaj `ALTER TABLE orders ADD COLUMN notes TEXT`; w przeciwnym
razie nic nie rób. Wielokrotne wywołanie funkcji musi być bezpieczne.

#### Go

Dodaj `migrateAddNotesColumn() error` do `db.go`. Wykonaj `PRAGMA
table_info(orders)` przez `Query` i odczytaj kolumnę `name` z każdego
wiersza. Jeśli brakuje `"notes"`, wykonaj `ALTER TABLE orders ADD COLUMN
notes TEXT`; w przeciwnym razie nic nie rób.

#### Java

Dodaj `migrateAddNotesColumn() throws SQLException` do `OrderDb.java`.
Wykonaj `PRAGMA table_info(orders)` i odczytaj kolumnę `name` każdego
wiersza przez `ResultSet.getString("name")`. Jeśli brakuje `"notes"`,
wykonaj `ALTER TABLE orders ADD COLUMN notes TEXT`; w przeciwnym razie
nic nie rób.

Nie dodawaj ORM-u ani narzędzia do migracji takiego jak Flyway czy
Liquibase. W tym ćwiczeniu całą migracją jest zabezpieczone sprawdzeniem
polecenie `ALTER TABLE`.

### Krok 9. Uruchamiaj migrację przy starcie

#### Python

W `run()` wywołaj `db.migrate_add_notes_column()` zaraz po
`db.init_db()`, żeby każdy start serwera zapewniał istnienie kolumny.

#### Go

W `main()` wywołaj `migrateAddNotesColumn()` zaraz po `initDB()`,
a ewentualny błąd obsłuż tak samo jak błąd `initDB()`.

#### Java

W `Main.java` wywołaj `OrderDb.migrateAddNotesColumn()` zaraz po
`OrderDb.initDb()`.

### Krok 10. Uruchamiaj migrację także w testach

Testy uruchamiają serwer bezpośrednio, z pominięciem `main` i `run()`.
Bez tego kroku test pola `notes` z kroku 16 skorzysta z bazy, która nie
przeszła migracji. Może zakończyć się mylącym błędem „no such column”
albo przejść wyłącznie przypadkiem.

#### Python

W funkcji przygotowującej testy w `tests/test_api.py` wywołaj
`db.migrate_add_notes_column()` zaraz po `db.init_db()`.

#### Go

W `newTestServer` wywołaj `migrateAddNotesColumn()` zaraz po
`initDB()`.

#### Java

W `@BeforeEach` w `ApiServerTest.java` wywołaj
`OrderDb.migrateAddNotesColumn()` zaraz po `OrderDb.initDb()`.

### Krok 11. Dodaj `notes` do warstwy przechowywania

#### Python

Zmień `create_order`, aby przyjmowało opcjonalny parametr
`notes: str = ""`, zapisywało go i zwracało. Dodaj `notes` do wyniku
`get_order`. Jeśli zapisana wartość to `NULL`, jak w wierszu z kroku 5,
zwróć `""`.

#### Go

Dodaj do `Order` pole `Notes string` z tagiem JSON `notes`. Zmień
`createOrder`, aby przyjmowało parametr `notes string`, zapisywało go
i zwracało. W `getOrder` odczytaj kolumnę `notes` za pomocą
`sql.NullString`. Przed przypisaniem jej do zwracanego `Order` zamień
`NULL` na `""`; taka wartość znajdzie się w wierszu z kroku 5.

#### Java

Dodaj do `Order` pole `final String notes` i parametr konstruktora.
Zmień `createOrder`, aby przyjmowało `String notes`, zapisywało go i
zwracało. W `getOrder` odczytaj kolumnę `notes` przez
`ResultSet.getString`. Metoda zwraca javowe `null` dla SQL-owego `NULL`,
dlatego w takim przypadku, występującym w wierszu z kroku 5, użyj `""`.

### Krok 12. Przyjmuj `notes` w ciele żądania

#### Python

Zmień `do_POST` w `api.py`, aby odczytywało opcjonalne pole
`notes` z ciała żądania (domyślnie `""`) i przekazywało je dalej do
`db.create_order`.

#### Go

Zmień `handleCreateOrder`, aby odczytywało opcjonalne pole
`notes` z zdekodowanego ciała (`body["notes"].(string)`, domyślnie
`""`, jeśli go brakuje albo nie jest napisem) i przekazywało je do
`createOrder`.

#### Java

Zmień `handleCreateOrder`, aby odczytywało opcjonalne pole `notes` z
przetworzonego ciała (`data.get("notes")`, domyślnie `""`, jeśli go
brakuje albo nie jest `String`) i przekazywało je do
`OrderDb.createOrder`.

### Krok 13. Uruchom ponownie i odczytaj historyczne zamówienie

Uruchom serwer ponownie z plikiem bazy używanym od kroku 5. Pobierz
utworzone wtedy zamówienie po jego rzeczywistym identyfikatorze. W
razie potrzeby zastąp poniższe `1`.

```bash
curl -s http://localhost:8000/orders/1
```

Żądanie musi nadal zakończyć się powodzeniem i zwrócić `notes` równe
`""`. Pole nie może zniknąć, a serwer nie może zakończyć się błędem.
W ten sposób migracja zostaje sprawdzona na wierszu utworzonym przed
dodaniem kolumny.

### Krok 14. Utwórz i pobierz nowe zamówienie z uwagami

Przy nadal działającym serwerze utwórz zamówienie z polem `notes`.

```bash
curl -s -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" \
  -d '{"items": ["Burger"], "notes": "no onions"}'
```

Zapisz identyfikator tego zamówienia, a następnie je pobierz. Przykład
używa `2`; zastąp tę wartość rzeczywistym identyfikatorem. Nie wklejaj
dosłownie symbolicznej wartości takiej jak `<new-order-id>`, ponieważ Bash
specjalnie interpretuje znaki `<` i `>`.

```bash
curl -s http://localhost:8000/orders/2
```

Potwierdź, że `notes` ma wartość `"no onions"`. Zatrzymaj serwer.

### Krok 15. Uruchom ponownie i sprawdź oba zamówienia

Uruchom serwer ostatni raz z tym samym plikiem bazy. Pobierz historyczne
zamówienie z kroku 5 i nowe z kroku 14. Oba muszą zachować swoje
wartości `notes`. Ten restart uruchamia migrację na bazie, która ma już
nową kolumnę, i potwierdza, że funkcja nie zgłasza błędu ani jej nie
duplikuje. Zatrzymaj serwer.

### Krok 16. Dodaj test nowego pola

#### Python

Dodaj test do `tests/test_api.py` potwierdzający, że `POST /orders` z
wartością `notes`, a potem `GET` na zwróconym id, zwraca tę samą
wartość `notes`.

#### Go

Dodaj do `api_test.go` test tego samego zachowania. Wzoruj się na
`TestPostThenGetOrder`.

#### Java

Dodaj do `ApiServerTest.java` test tego samego zachowania. Wzoruj się
na `postThenGetOrder`.

### Krok 17. Zaktualizuj kontrakt

Zaktualizuj `CONTRACT.md` z laba 21. Ciało żądania `POST /orders`
przyjmuje teraz opcjonalne pole `notes`. Każda odpowiedź zawierająca
zamówienie, zarówno udana, jak i błędna, musi uwzględniać `notes`.
Przykład:

**Żądanie** `{"items": ["Burger"], "notes": "no onions"}`

**Odpowiedź** `{"order_id": "4", "items": ["Burger"], "status":
"received", "notes": "no onions"}`

Wartość `order_id` zależy od liczby zamówień utworzonych w tej sesji,
więc nie zakładaj, że zawsze wynosi `"1"`. Jeśli w żądaniu nie ma
`notes` albo zamówienie powstało przed migracją, odpowiedź musi zawierać
`""`, a nie pominięte pole lub `null`.

### Krok 18. Otwórz i scal pull request

Wykonaj pracę z tego laba na gałęzi, na przykład
`feature/data-outlives-code`, wypchnij ją i otwórz pull request.
Scal go dopiero po uzyskaniu zielonego wyniku CI z laba 21. Przez cały
Akt V stosuj ten sam proces: gałąź, pull request, zielone CI i scalenie.

## Kryteria akceptacji

- Istnieje warstwa przechowywania oparta na SQLite (`db.py` dla
  Pythona, `db.go` dla Go, `OrderDb.java` dla Javy), która obsługuje
  tworzenie schematu, migrację `notes` oraz zapis i odczyt zamówień.
- Wszystkie testy z laba 21 nadal przechodzą, plus nowy test `notes` (5
  razem).
- Zamówienie utworzone przed uruchomieniem migracji `notes` jest
  nadal pobieralne po niej, z `notes == ""`.
- Nowe zamówienie zachowuje wartość `notes` po `POST`, `GET` i ponownym
  uruchomieniu serwera.
- Uruchomienie migracji na bazie z nową kolumną nie zgłasza błędu ani nie
  zmienia istniejących danych.
- `CONTRACT.md` odzwierciedla nowe opcjonalne pole `notes`.
- Zmiany z tego laba zostały scalone przez pull request z zielonym
  wynikiem CI, a nie zapisane bezpośrednio na `main`.

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

Oczekuj 5 przechodzących testów lub odpowiednika w Gradle. Nadal
wymagane są ręczne kontrole z kroków 5, 6, 13, 14 i 15, ponieważ zestaw
testów nie zastąpi prawdziwych restartów z istniejącą bazą.

## Zastanów się

- Twoja migracja użyła `ALTER TABLE ... ADD COLUMN` bez klauzuli
  domyślnej, więc istniejące wiersze dostają `NULL`. Dlaczego trzeba
  obsłużyć ten `NULL` w kodzie, zamiast naprawić go raz w bazie danych?
- Część 1 (SQLite zamiast stanu w pamięci) w ogóle nie zmieniła
  `CONTRACT.md`. Część 2 (`notes`) zmieniła. Jaka jest różnica między
  tymi dwoma rodzajami zmian, z punktu widzenia wywołującego?
- Migracja jest sprawdzana przy każdym uruchomieniu. Co mogłoby się
  stać, gdyby wymagała osobnego polecenia, o którym inna osoba z zespołu
  zapomniałaby po pobraniu zmian?

## Jeśli utkniesz

### Python

- **Podpowiedź 1.** `sqlite3.connect(path)` otwiera i w razie potrzeby
  tworzy plik bazy danych. `conn.row_factory = sqlite3.Row` pozwala
  odczytywać kolumny po nazwie (`row["items"]`) zamiast po
  indeksie.
- **Podpowiedź 2.** Po `INSERT` właściwość `cur.lastrowid` zawiera
  automatycznie wygenerowany `order_id`.
- **Podpowiedź 3.** `PRAGMA table_info(orders)` zwraca po jednym
  wierszu na kolumnę, każdy z polem `name`. Przejdź po nich pętlą, aby
  sprawdzić, czy `notes` już istnieje, zanim spróbujesz dodać ją
  ponownie.

### Go

- **Podpowiedź 1.** Zarejestruj sterownik pustym importem
  `import _ "modernc.org/sqlite"`, a następnie wywołaj
  `sql.Open("sqlite", path)`. `sql.Open` nie nawiązuje jeszcze połączenia,
  więc błąd pojawi się dopiero przy pierwszym zapytaniu. Aby wcześniej
  wykryć nieprawidłową ścieżkę, od razu wywołaj `db.Ping()`.
- **Podpowiedź 2.** `result, err := conn.Exec(...)`, a następnie
  `result.LastInsertId()`, zwraca automatycznie wygenerowany `order_id`
  jako `int64`. Przekształć go przez `strconv.FormatInt(id, 10)`.
- **Podpowiedź 3.** `rows, err := conn.Query("PRAGMA table_info(orders)")`
  zwraca po jednym wierszu na kolumnę; `rows.Scan` potrzebuje
  miejsca docelowego dla każdej kolumny, którą zwraca `PRAGMA
  table_info` (`cid`, `name`, `type`, `notnull`, `dflt_value`, `pk`),
  nawet jeśli interesuje Cię tylko `name`.
- **Podpowiedź 4.** Pole `.Valid` w `sql.NullString` ma wartość `false`,
  gdy baza zwróciła `NULL`. Taki przypadek zamień na `""`.

### Java

- **Podpowiedź 1.** `DriverManager.getConnection("jdbc:sqlite:" + path)`
  otwiera i w razie potrzeby tworzy plik bazy. sqlite-jdbc rejestruje
  się automatycznie, więc nie potrzebujesz
  `Class.forName(...)`.
- **Podpowiedź 2.**
  `statement.executeUpdate(sql, Statement.RETURN_GENERATED_KEYS)`, a
  potem `statement.getGeneratedKeys()`, zwraca `ResultSet` z automatycznie
  wygenerowanym `order_id` w pierwszej kolumnie.
- **Podpowiedź 3.** `ResultSet.getString("name")` dla każdego wiersza
  `PRAGMA table_info(orders)` zwraca nazwę kolumny. Użyj pętli
  `while (rs.next())`, aby sprawdzić, czy `"notes"` już istnieje.
- **Podpowiedź 4.** `ResultSet.getString("notes")` zwraca javowe `null`
  dla SQL-owego `NULL`. Nie zgłasza wyjątku ani nie zwraca specjalnej
  wartości. Sprawdź wynik przed przekazaniem go do serializatora JSON.

## Co dalej

Dane przetrwają teraz ponowne uruchomienia i zmiany schematu w każdej
ścieżce. Następnie kuchnia chce powiadomić zewnętrzny serwis dostawy,
który nie zawsze odpowiada.

Przejdź do [Lab 23 — Świat zewnętrzny zawodzi](../23-outside-world-fails/README.pl.md).
