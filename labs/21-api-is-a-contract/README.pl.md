# Lab 21 — API to kontrakt

## Sytuacja

Inna część systemu kuchennego musi tworzyć zamówienia i sprawdzać ich
stan przez sieć. Może być napisana w innym języku i nie może importować
Twojego kodu. Obie strony potrzebują interfejsu, który mogą uzgodnić bez
czytania nawzajem swojego kodu źródłowego.

## Cele nauki

Po tym labie potrafisz:

- Opisać kontrakt endpointu HTTP, w tym strukturę żądań i odpowiedzi,
  kody statusu oraz format błędów.
- Wyjaśnić, dlaczego kontrakt musi opisywać błędy, a nie tylko udane
  przypadki.
- Dodać nową regułę walidacji do istniejącego endpointu bez zmiany
  zachowania dla klientów, którzy już przestrzegają kontraktu.

## Zanim zaczniesz

- Ukończ laby 06-20 w wybranej ścieżce.
- Jeśli projekt został już rozpoczęty w starej lokalizacji
  (`examples/order-api/api.py`, bez podfolderu `python/`), nic nie
  jest stracone. Przenieś utworzone pliki do
  `examples/order-api/python/`, na przykład poleceniem
  `mkdir -p examples/order-api/python && git mv examples/order-api/api.py examples/order-api/python/`,
  dostosowanym do dodanych plików. Następnie kontynuuj od tego miejsca.
  Nie używaj `git reset --hard` ani `git clean`, aby „zacząć od nowa”.
  Oba polecenia usunęłyby pracę, która nie została jeszcze wypchnięta.

### Python

- Pracuj w `examples/order-api/python/`.
- Sprawdź działanie projektu poleceniem `uv run pytest -v`.

### Go

- Pracuj w `examples/order-api/go/`.
- Sprawdź działanie projektu poleceniem `go test ./...`.

### Java

- Pracuj w `examples/order-api/java/`.
- Sprawdź działanie projektu poleceniem `./gradlew test`.

## Twoje zadanie

### Krok 1. Uruchom serwer i sprawdź go ręcznie

Otwórz dwa terminale, jeden dla serwera i drugi dla poleceń `curl`.
W VS Code utwórz pierwszy przez **Terminal → New Terminal**, a następnie
kliknij ikonę **split terminal** lub ponownie wybierz **Terminal → New
Terminal**. Oba pozostaną w tym samym oknie programu.

W **Terminalu A** uruchom serwer i pozostaw go włączonego.

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

Pierwsze uruchomienie pobiera dystrybucję Gradle i zależności, jeśli
nie ma ich jeszcze w pamięci podręcznej. Może to potrwać minutę lub
dwie. Kolejne uruchomienia będą szybsze.

**Wszystkie trzy ścieżki** po uruchomieniu wypisują tę samą linię.

```text
order-api listening on http://localhost:8000
```

Ta linia potwierdza, że serwer działa. Zaczekaj na nią przed przejściem
do Terminala B.

W **Terminalu B** ręcznie sprawdź serwer działający w Terminalu A.
Poniższe polecenia są takie same w każdej ścieżce, ponieważ korzystają
wyłącznie z HTTP.

```bash
curl -i -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" \
  -d '{"items": ["Burger", "Fries"]}'
curl -i http://localhost:8000/orders/1
curl -i http://localhost:8000/orders/999
```

Przed przejściem do kolejnego polecenia przeczytaj linię statusu i ciało
każdej odpowiedzi.

**Jeśli `curl` zgłasza** `curl: (7) Failed to connect to localhost port
8000... Couldn't connect to server`, serwer w Terminalu A nie działa,
zakończył się błędem albo korzysta z innego portu. Sprawdź komunikaty w
Terminalu A i zaczekaj na linię „listening”, zanim spróbujesz ponownie.

**Jeśli Terminal A zgłasza, że adres jest już używany**, inny proces
nasłuchuje na porcie 8000. Najczęściej jest to wcześniej uruchomiony
serwer. Znajdź go w jeden z poniższych sposobów.

- Poszukaj wcześniejszej karty lub panelu terminala, w którym nadal
  działa serwer.
- Na macOS lub Linuksie uruchom `lsof -i :8000`. W systemie Windows
  sprawdź w Menedżerze zadań, czy pozostał proces `python`, `go` lub
  `java`, i go zatrzymaj.
- W ostateczności użyj zmiennej środowiskowej `PORT`, którą obsługuje
  serwer w każdej ścieżce. Uruchom na przykład
  `PORT=8001 uv run python api.py` albo dodaj tę samą zmienną do polecenia
  `go run .` lub `./gradlew run`. Następnie w poleceniach `curl` użyj
  adresu `http://localhost:8001`.

Gdy skończysz badanie, zatrzymaj serwer w Terminalu A przez `Ctrl+C`.

#### Python

Terminal A wyświetli traceback `KeyboardInterrupt`. To oczekiwany i
niegroźny sposób informowania, że `Ctrl+C` przerwał blokujące wywołanie.
Serwer został zatrzymany.

#### Go

Proces zakończy się bez dodatkowego komunikatu. To normalne zachowanie
programu Go bez specjalnej obsługi zamykania.

#### Java

Proces zakończy się, a Gradle może poinformować o przerwaniu kompilacji.
W obu przypadkach serwer został zatrzymany.

### Krok 2. Opisz kontrakt

Utwórz `examples/order-api/CONTRACT.md`. Dla każdego endpointu opisz
metodę i ścieżkę HTTP, ciało żądania, jeśli występuje, wszystkie możliwe
odpowiedzi wraz z kodem statusu i ciałem oraz przyczynę każdego błędu.
Kontrakt jest wspólny dla wszystkich trzech ścieżek, ponieważ nie zależy
od języka implementacji.

Poniższy przykład pokazuje strukturę opisu jednego endpointu. Pozostałe
uzupełnij na podstawie obserwacji z kroku 1, zamiast zgadywać.

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

Zrób to samo dla `GET /orders/{id}`, zarówno dla istniejącego, jak i
nieznanego identyfikatora, oraz dla nieznanej ścieżki. Przed opisaniem
każdego przypadku sprawdź prawdziwą odpowiedź serwera.

### Krok 3. Dodaj regułę walidacji

Pojawia się nowy wymóg. **Każdy element w `items` musi być niepustym
napisem.** Liczba, pusty napis lub `null` na liście mają zostać odrzucone
tak samo jak brakująca lista `items`.

#### Python

W metodzie `do_POST` w `api.py`, zaraz po sprawdzeniu, czy `items` jest
niepustą listą, i przed utworzeniem zamówienia dodaj kolejną walidację.
Jeśli którykolwiek element nie jest niepustym napisem, odpowiedz
`400 {"error": "each item must be a non-empty string"}`.

#### Go

W `handleCreateOrder` w `api.go`, zaraz po sprawdzeniu
`len(items) == 0` i przed utworzeniem zamówienia, dodaj pętlę po
elementach. Gdy znajdzie element, który nie jest niepustym napisem,
powinna odpowiedzieć `400 {"error": "each item must be a non-empty
string"}`. Elementy zdekodowanego JSON-u mają typ `any`, dlatego przed
sprawdzeniem zawartości potrzebna będzie asercja typu `string`.

#### Java

W `handleCreateOrder` w `ApiServer.java`, zaraz po sprawdzeniu
`items.isEmpty()` i przed utworzeniem zamówienia, dodaj pętlę po
elementach. Gdy znajdzie element, który nie jest niepustym napisem,
powinna odpowiedzieć `400 {"error": "each item must be a non-empty
string"}`. Elementy przetworzonego JSON-u mają typ `Object`, dlatego
przed sprawdzeniem zawartości użyj `instanceof`.

### Krok 4. Dodaj test

#### Python

Dodaj do `tests/test_api.py` test potwierdzający, że `POST /orders` z
`{"items": ["Burger", ""]}` lub innym niepoprawnym elementem zwraca
`400`.

#### Go

Dodaj do `api_test.go` test potwierdzający, że `POST /orders` z
`{"items": ["Burger", ""]}` zwraca `400`. Wzoruj się na
`TestPostWithoutItemsReturns400`.

#### Java

Dodaj do `ApiServerTest.java` test potwierdzający, że `POST /orders`
z `{"items": ["Burger", ""]}` zwraca `400`. Wzoruj się na
`postWithoutItemsReturns400`.

### Krok 5. Zaktualizuj kontrakt

Dodaj nowy przypadek błędu do `CONTRACT.md`, pod listą odpowiedzi
`POST /orders`.

### Krok 6. Skonfiguruj CI

Utwórz `.github/workflows/order-api-ci.yml` na podstawie
`team-inventory-ci.yml` z laba 19. Nowy przepływ ma być uruchamiany przez
`[push, pull_request]`.

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

Ten przepływ uruchamia testy tylko w wybranej ścieżce. Nie musi
konfigurować Pythona, Go *i* Javy dla jednego projektu. Przepływ
utrzymaniowy repozytorium, `.github/workflows/course-health.yml`,
konfiguruje wszystkie trzy języki, ponieważ sprawdza cały kurs.

### Krok 7. Otwórz i scal pull request

Wykonaj pracę z tego laba na osobnej gałęzi, na przykład
`feature/api-contract`. Wypchnij ją i otwórz pull request tak jak w labie
18. Zacznij w interfejsie GitHuba i przed utworzeniem pull requesta
sprawdź repozytorium oraz gałąź docelową. W zakładce **Checks** upewnij
się, że nowy przepływ `order-api CI` zakończył się powodzeniem. Sam
zielony wynik istniejącego `team-inventory CI` z laba 19 nie wystarczy.
Następnie scal pull request i pobierz zmianę do lokalnego `main`.

## Kryteria akceptacji

- `examples/order-api/CONTRACT.md` istnieje i dokumentuje każdy
  endpoint, każdy kod statusu, który może zwrócić, i co wyzwala
  każdy z nich, w tym nowy błąd walidacji elementów.
- Nowa reguła walidacji elementów jest zaimplementowana i ma
  przechodzący test.
- Komenda testowa Twojej ścieżki przechodzi z oryginalnymi trzema
  testami plus Twoim nowym (4 łącznie).
- `.github/workflows/order-api-ci.yml` istnieje, uruchamia się po
  wypchnięciu zmian oraz dla pull requestów i wykonuje testy w
  `examples/order-api/<twój-język>`.
- Zmiany z tego laba zostały scalone przez pull request z zielonym
  wynikiem CI, a nie zapisane bezpośrednio na `main`.

## Weryfikacja

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

Oczekuj `4 passed` lub odpowiednika w Gradle, komunikatów `contract
documented` i `CI workflow exists` oraz zielonego wyniku na pull
requeście użytym do scalenia zmian.

## Zastanów się

- Czy przeniesienie `items` w odpowiedzi na udany `POST` pod nowy klucz
  `"order"` zepsułoby klienta napisanego według obecnego `CONTRACT.md`?
  Czy taki sam skutek miałoby dodanie nowego, opcjonalnego pola?
- Twoja reguła walidacji dla elementów `items` jest nowa. Czy klient,
  który już wysyłał poprawne dane, zauważyłby tę zmianę?

## Jeśli utkniesz

### Python

- **Podpowiedź 1.** `curl -i` pokazuje linię statusu i nagłówki
  odpowiedzi, a nie tylko jej ciało. Użyj ich do ręcznego sprawdzenia
  kodów statusu.
- **Podpowiedź 2.** Nową walidację umieść w `do_POST`, zaraz po
  sprawdzeniu, czy `items` jest niepustą listą, i przed utworzeniem
  zamówienia.
- **Podpowiedź 3.** Jednym ze sposobów sprawdzenia, czy każdy element
  jest niepustym napisem, jest `all(isinstance(item, str) and
  item.strip() for item in items)`.

### Go

- **Podpowiedź 1.** `curl -i` pokazuje linię statusu i nagłówki
  odpowiedzi, a nie tylko jej ciało. Użyj ich do ręcznego sprawdzenia
  kodów statusu.
- **Podpowiedź 2.** Nową walidację umieść w `handleCreateOrder`, zaraz
  po sprawdzeniu `len(items) == 0` i przed utworzeniem zamówienia.
- **Podpowiedź 3.** `item.(string)` jest asercją typu. Jej
  dwuwartościowa postać, `s, ok := item.(string)`, informuje, czy element
  jest napisem, i udostępnia go do sprawdzenia przez
  `strings.TrimSpace(s) == ""`.

### Java

- **Podpowiedź 1.** `curl -i` pokazuje linię statusu i nagłówki
  odpowiedzi, a nie tylko jej ciało. Użyj ich do ręcznego sprawdzenia
  kodów statusu.
- **Podpowiedź 2.** Nową walidację umieść w `handleCreateOrder`, zaraz
  po sprawdzeniu `items.isEmpty()` i przed utworzeniem zamówienia.
- **Podpowiedź 3.** Dopasowanie wzorca `instanceof` w Javie, na przykład
  `if (item instanceof String s)`, jednocześnie sprawdza typ i tworzy
  zmienną `String`. Następnie możesz użyć `s.trim().isEmpty()`.

Przed przejściem dalej utwórz commit ze wszystkimi zmianami z tego laba
i wypchnij go (`git add -A && git commit -m "..."; git push`). Nadal
stosuj proces z Aktu IV: gałąź, pull request, zielone CI i scalenie.
Każda zmiana w Akcie V przechodzi tę drogę, tym razem w `order-api`.

## Co dalej

API działa do chwili restartu, po którym znikają wszystkie zamówienia.
W kolejnym labie dane mają przetrwać w Pythonie, Go i Javie.

Przejdź do [Lab 22 — Kod się zmienił, stare dane zostały](../22-data-outlives-code/README.pl.md).
