# Lab 06 — Od skryptu do projektu

## Sytuacja

Program w `examples/restaurant-bill/` oblicza rachunek w restauracji:
wartość pozycji, rabat lojalnościowy, podatek, napiwek i kwotę końcową.
Działa, ale jedna funkcja (lub jedno `main` w Go i Javie) wykonuje pięć
różnych zadań. Zmiana jednej części wymaga przejrzenia całego programu.

W Labie 05 wybrałeś/aś ścieżkę Python, Go albo Java. Gdy instrukcja
dzieli się na warianty, czytaj tylko sekcję dla swojego języka. Każdy
starter rozwiązuje ten sam problem na tych samych danych i prowadzi do
równoważnej struktury projektu.

## Cele nauki

Po tym labie potrafisz:

- Rozdzielić odrębne odpowiedzialności skryptu na osobne moduły
  (Python), pakiety (Go) albo klasy (Java).
- Wyjaśnić różnicę między czystą funkcją obliczającą a punktem wejścia,
  który obsługuje wejście i wyjście, w tym przypadku wyświetlanie.
- Opisać projekt narzędziom danego języka za pomocą manifestu
  (`pyproject.toml`, `go.mod` albo `build.gradle`), zamiast pozostawiać
  go jako luźny zbiór plików.

## Zanim zaczniesz

- Laby 01-04 ukończone.
- Kontynuuj ścieżkę Python, Go albo Java wybraną w Labie 05. Każda z
  nich jest kompletną drogą przez Laby 06-10.
- Jeśli już zacząłeś/aś ten projekt w jego starej lokalizacji
  (`examples/restaurant-bill/bill.py`, bez podkatalogu `python/`),
  możesz zachować dotychczasową pracę. Przenieś utworzone pliki do
  `examples/restaurant-bill/python/`, na przykład poleceniem
  `mkdir -p examples/restaurant-bill/python && git mv examples/restaurant-bill/bill.py examples/restaurant-bill/python/`,
  dostosowując je do samodzielnie dodanych plików. Nie uruchamiaj
  `git reset --hard` ani `git clean`, aby zacząć od nowa. Oba polecenia
  mogą usunąć pracę, której jeszcze nie ma w zdalnym repozytorium.

### Python

- Wszystkie poniższe polecenia uruchamiaj w
  `examples/restaurant-bill/python/`.
- `uv` zainstalowany (Lab 05 to obejmuje).
- Przeczytaj `bill.py` w całości, zanim coś zmienisz.

### Go

- Wszystkie poniższe polecenia uruchamiaj w
  `examples/restaurant-bill/go/`.
- Zainstalowane Go 1.27.x. Sprawdź je poleceniem `go version`. Jeśli go
  nie ma, skorzystaj z oficjalnej instrukcji na
  [go.dev/doc/install](https://go.dev/doc/install). Ten kurs nie używa
  menedżera wersji Go. Po ukończeniu Labu 05 na tym samym komputerze
  środowisko jest już gotowe.
- Przeczytaj `main.go` w całości, zanim coś zmienisz.

### Java

- Wszystkie poniższe polecenia uruchamiaj w
  `examples/restaurant-bill/java/`.
- Zainstalowany JDK 21. Sprawdź go poleceniem `java -version`; wynik
  powinien zawierać `21`. Jeśli go nie ma, zainstaluj Temurin 21 od
  Adoptium z [adoptium.net](https://adoptium.net/temurin/releases/?version=21).
  Globalna instalacja Gradle nie jest potrzebna, ponieważ projekt
  zawiera Gradle Wrapper (`./gradlew`). Po ukończeniu Labu 05 na tym
  samym komputerze środowisko jest już gotowe.
- Przeczytaj `src/main/java/Main.java` w całości, zanim coś zmienisz.

## Twoje zadanie

### Python

1. Uruchom `python3 bill.py` (Windows w Git Bash: `python bill.py`;
   oficjalny instalator Pythona dla Windows dostarcza `python`, nie
   `python3`), zapisując jego wynik jako punkt odniesienia do późniejszego
   porównania: `python3 bill.py | tee /tmp/bill-before.txt` (`python
   bill.py | tee /tmp/bill-before.txt` na Windows).
2. Wskaż zadania połączone w `main()`: obliczanie sumy częściowej,
   rabatu, podatku i napiwku oraz wyświetlanie rachunku.
3. Utwórz `pyproject.toml`. Ustaw nazwę na `restaurant-bill`, dodaj
   `requires-python = ">=3.13"`, zależność deweloperską `pytest` oraz
   sekcję `[tool.pytest.ini_options]` z `pythonpath = ["."]`, tak jak w
   Labie 05.
4. Utwórz pakiet `billing/` (`billing/__init__.py`, pusty) z modułem
   `billing/calculator.py` zawierającym pięć czystych funkcji o podanych
   nazwach i sygnaturach. Kolejne dwa laby będą z nich korzystać.
   - `calculate_subtotal(items: list[tuple[str, float, int]]) -> float`
     zwraca sumę `price * quantity` dla wszystkich pozycji.
   - `calculate_discount(subtotal: float) -> float` zwraca 10%
     `subtotal`, jeśli `subtotal >= 50`, a w przeciwnym razie `0`.
   - `calculate_tax(amount: float) -> float` zwraca 8% `amount`.
   - `calculate_tip(amount: float, tip_rate: float) -> float` zwraca
     `amount * tip_rate`.
   - `calculate_bill(items: list[tuple[str, float, int]], tip_rate: float)
     -> dict[str, float]` łączy cztery powyższe funkcje w słownik z
     kluczami `subtotal`, `discount`, `tax`, `tip`, `total`. **Na razie
     policz `tax` z całego `subtotal`, tak jak oryginalny skrypt**.
     Refaktoryzacja ma zachować obecne działanie, w tym błędy. Na razie
     niczego nie naprawiaj.
5. Utwórz `billing/cli.py` z funkcją `main()`, która wywołuje
   `calculate_bill` *raz* i wyświetla rachunek identyczny bajt po bajcie
   z wynikiem oryginalnego skryptu, używając tylko wartości ze
   zwróconego słownika. Nie przeliczaj żadnej wartości osobno.
6. Utwórz `main.py` w katalogu głównym projektu, który importuje `main`
   z `billing.cli` i wywołuje ją pod `if __name__ == "__main__":`.
7. Uruchom swój nowy punkt wejścia tą samą metodą, zapisując też jego
   wynik: `uv run python main.py | tee /tmp/bill-after.txt`. Porównaj
   oba pliki: `diff /tmp/bill-before.txt /tmp/bill-after.txt`.
8. Gdy diff jest pusty, usuń `bill.py`; został w pełni zastąpiony.

### Go

1. Uruchom `go run main.go`, zapisując jego wynik jako punkt odniesienia:
   `go run main.go | tee /tmp/bill-before.txt`. Zauważ, że to działa bez
   `go.mod`. Polecenie `go run` z nazwą pliku nie wymaga modułu, podobnie
   jak `python3 bill.py` nie wymaga manifestu projektu.
2. Wskaż zadania połączone w `main()`: obliczanie sumy częściowej,
   rabatu, podatku i napiwku oraz wyświetlanie rachunku.
3. Utwórz moduł poleceniem `go mod init restaurant-bill`. Powstanie
   `go.mod`, odpowiednik `pyproject.toml` w Go, który nada projektowi
   ścieżkę modułu.
4. Utwórz pakiet `billing` w nowym podkatalogu `billing/`, z plikiem
   `billing/calculator.go` (`package billing`) zawierającym
   pięć eksportowanych funkcji i dwa typy o podanych nazwach. Kolejne
   dwa laby będą z nich korzystać.
   - `type Item struct { Name string; Price float64; Qty int }`
   - `type Bill struct { Subtotal, Discount, Tax, Tip, Total float64 }`
   - `func CalculateSubtotal(items []Item) float64` zwraca sumę
     `Price * float64(Qty)` dla każdej pozycji.
   - `func CalculateDiscount(subtotal float64) float64` zwraca 10%
     `subtotal`, jeśli `subtotal >= 50`, a w przeciwnym razie `0`.
   - `func CalculateTax(amount float64) float64` zwraca 8% `amount`.
   - `func CalculateTip(amount, tipRate float64) float64` zwraca
     `amount * tipRate`.
   - `func CalculateBill(items []Item, tipRate float64) Bill` łączy
     cztery powyższe funkcje w `Bill`. **Na razie policz `Tax` z całego
     `subtotal`, tak jak oryginalny program**. Zachowaj obecny błąd;
     na razie nie zmieniaj działania programu.
5. Utwórz `billing/cli.go` (ten sam pakiet) z funkcją
   `func Run(items []Item, tipRate float64)`, która wywołuje
   `CalculateBill` *raz* i wyświetla rachunek identyczny bajt po bajcie z
   wynikiem oryginalnego programu, używając tylko pól zwróconego
   `Bill`.
6. Przepisz `main.go` w katalogu głównym tak, aby `func main()` tworzyła
   slice `items`, a następnie wywoływała `billing.Run(items, 0.15)`.
   Zaimportuj pakiet `billing` przez jego ścieżkę modułu:
   `import "restaurant-bill/billing"`.
7. Uruchom punkt wejścia po refaktoryzacji tą samą metodą,
   zapisując też jego wynik: `go run main.go | tee /tmp/bill-after.txt`.
   Porównaj oba pliki: `diff /tmp/bill-before.txt /tmp/bill-after.txt`.
8. Gdy diff jest pusty, potwierdź, że `go run .`, czyli wariant
   korzystający z modułu, daje ten sam wynik. Polecenie działa teraz,
   ponieważ istnieje `go.mod`.

### Java

1. Uruchom starter, zapisując jego wynik jako punkt odniesienia:
   `./gradlew run --console=plain | tee /tmp/bill-before.txt`. Pierwsze
   uruchomienie pobiera dystrybucję Gradle i zależności, jeśli nie są
   jeszcze w pamięci podręcznej. Może to zająć minutę lub dwie. Kolejne
   uruchomienia będą szybsze.
2. Wskaż zadania połączone w `main()`: obliczanie sumy częściowej,
   rabatu, podatku i napiwku oraz wyświetlanie rachunku.
3. Utwórz pakiet `billing` z nowym plikiem
   `src/main/java/billing/Calculator.java` (`package billing;`)
   zawierającym dwa zagnieżdżone typy i pięć statycznych metod o
   podanych nazwach. Kolejne dwa laby będą z nich korzystać.
   - `public static class Item` z publicznymi polami `name` (`String`),
     `price` (`double`), `qty` (`int`) i odpowiadającym konstruktorem.
   - `public static class Bill` z publicznymi polami `subtotal`,
     `discount`, `tax`, `tip`, `total` (wszystkie `double`) i
     odpowiadającym konstruktorem.
   - `public static double calculateSubtotal(List<Item> items)` zwraca sumę
     `price * qty` dla każdej pozycji.
   - `public static double calculateDiscount(double subtotal)` zwraca
     10% `subtotal`, jeśli `subtotal >= 50`, a w przeciwnym razie `0`.
   - `public static double calculateTax(double amount)` zwraca 8%
     `amount`.
   - `public static double calculateTip(double amount, double tipRate)`
     zwraca `amount * tipRate`.
   - `public static Bill calculateBill(List<Item> items, double tipRate)`
     łączy cztery powyższe metody w `Bill`. **Na razie policz `tax` z
     całego `subtotal`, tak jak oryginalny program**. Zachowaj obecny
     błąd; na razie nie zmieniaj działania programu.
4. Utwórz `src/main/java/billing/Cli.java` (ten sam pakiet) z metodą
   `public static void run(List<Calculator.Item> items, double tipRate)`,
   która wywołuje `Calculator.calculateBill` *raz* i wyświetla rachunek
   identyczny bajt po bajcie z wynikiem oryginalnego programu, używając
   tylko pól zwróconego `Bill`.
5. Przepisz `src/main/java/Main.java` tak, aby metoda `main` tworzyła
   listę `items`, a następnie wywoływała `Cli.run(items, 0.15)`.
   Potrzebne będą `import billing.Calculator;` oraz
   `import billing.Cli;`.
6. Dodaj JUnit 5 do `build.gradle`, przygotowując projekt do Labu 07.
   Dodaj blok `repositories { mavenCentral() }` i te zależności:
   ```gradle
   dependencies {
       testImplementation platform('org.junit:junit-bom:5.11.0')
       testImplementation 'org.junit.jupiter:junit-jupiter'
       testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
   }
   ```
   Dodaj również blok `test { useJUnitPlatform() }`. Nie ma jeszcze
   pliku testowego, dlatego `./gradlew test` kończy się sukcesem mimo
   braku testów.
7. Uruchom punkt wejścia po refaktoryzacji tą samą metodą, zapisując
   też jego wynik: `./gradlew run --console=plain | tee /tmp/bill-after.txt`.
   Porównaj oba pliki, pomijając komunikaty Gradle, które nie są częścią
   wyniku programu. Możesz porównać tylko fragment od `Receipt` albo
   bezpośrednio zestawić wyniki
   `./gradlew run --console=plain --quiet`.

## Kryteria akceptacji

### Python

- `examples/restaurant-bill/python/bill.py` już nie istnieje.
- `uv run python main.py` daje wynik *identyczny* z wynikiem
  oryginalnego skryptu.
- `billing/calculator.py` definiuje wszystkie pięć funkcji z dokładnymi
  nazwami i sygnaturami wymienionymi powyżej.

### Go

- `examples/restaurant-bill/go/go.mod` istnieje, deklarując moduł
  `restaurant-bill`.
- `go run .` daje wynik *identyczny* z wynikiem oryginalnego
  programu.
- `billing/calculator.go` definiuje typy `Item` i `Bill` oraz wszystkie pięć
  funkcji z dokładnymi nazwami i sygnaturami wymienionymi powyżej.

### Java

- `examples/restaurant-bill/java/build.gradle` deklaruje zależności
  JUnit 5 i `useJUnitPlatform()`.
- `./gradlew run` daje wynik *identyczny* z wynikiem oryginalnego
  programu.
- `billing/Calculator.java` definiuje typy `Item` i `Bill` oraz wszystkie
  pięć metod z dokładnymi nazwami i sygnaturami wymienionymi powyżej.

## Weryfikacja

### Python

```bash
cd examples/restaurant-bill/python
uv run python main.py | tee /tmp/bill-after.txt
diff /tmp/bill-before.txt /tmp/bill-after.txt && echo "IDENTICAL"
test -f bill.py && echo "ERROR: bill.py still exists" || echo "bill.py correctly removed"
cd -
```

Oczekiwane: `IDENTICAL` i `bill.py correctly removed`.

### Go

```bash
cd examples/restaurant-bill/go
go run . | tee /tmp/bill-after.txt
diff /tmp/bill-before.txt /tmp/bill-after.txt && echo "IDENTICAL"
test -f go.mod && echo "go.mod exists" || echo "ERROR: go.mod missing; run go mod init"
cd -
```

Oczekiwane: `IDENTICAL` i `go.mod exists`.

### Java

```bash
cd examples/restaurant-bill/java
./gradlew run --console=plain --quiet | tee /tmp/bill-after.txt
diff /tmp/bill-before.txt /tmp/bill-after.txt && echo "IDENTICAL"
./gradlew test
cd -
```

Oczekiwany wynik: `IDENTICAL` oraz zakończone sukcesem `./gradlew test`.
Brak testów jest zgodny z założeniami do Labu 07.

## Zastanów się

- Ręczny `diff` potwierdził, że refaktoryzacja nie zmieniła działania.
  Co trzeba byłoby powtarzać po każdej kolejnej zmianie bez testu
  automatycznego?
- Funkcja obliczająca podatek potrzebuje tylko jednej liczby. Dlaczego
  taka cecha jest przydatna w funkcji lub metodzie?

## Jeśli utkniesz

### Python

- **Podpowiedź 1:** Pięć funkcji w `calculator.py`, jedna funkcja w
  `cli.py`, jeden dwuliniowy `main.py`. To cała struktura.
- **Podpowiedź 2:** `calculate_bill` powinno wywoływać cztery inne
  funkcje. Nie powielaj ich logiki wewnątrz tej funkcji.
- **Podpowiedź 3:** Jeśli Twój diff nie jest pusty, wypisz oba pliki
  przez `cat -A` albo porównaj je linia po linii. Częstym źródłem
  drobnych różnic jest formatowanie liczb zmiennoprzecinkowych (`.2f`).

### Go

- **Podpowiedź 1:** Pięć funkcji plus dwa typy w `calculator.go`, jedna
  funkcja w `cli.go`, dwuliniowe `main()`. To cała struktura.
- **Podpowiedź 2:** `CalculateBill` powinno wywoływać cztery inne
  funkcje. Nie powielaj ich logiki wewnątrz tej funkcji.
- **Podpowiedź 3:** Ścieżka importu dla Twojego własnego pakietu to
  `"<nazwa modułu z go.mod>/billing"`. Jeśli `go run .` nie może go
  znaleźć, sprawdź, czy pierwsza linia `go.mod` odpowiada
  temu, co importujesz.

### Java

- **Podpowiedź 1:** Dwa zagnieżdżone typy plus pięć statycznych metod w
  `Calculator.java`, jedna statyczna metoda w `Cli.java`, dwuliniowe
  `main`. To cała struktura.
- **Podpowiedź 2:** `calculateBill` powinno wywoływać cztery inne
  metody. Nie powielaj ich logiki wewnątrz tej metody.
- **Podpowiedź 3:** Jeśli `./gradlew run` wydaje się wisieć pierwszy
  raz, prawdopodobnie pobiera dystrybucję Gradle i zależności. Zaczekaj
  na zakończenie; kolejne uruchomienia będą szybsze.

Przed przejściem dalej zacommituj i wypchnij wszystkie zmiany poleceniem
`git add -A && git commit -m "..."; git push`. Kolejne laby nie wymagają
jeszcze czystego drzewa, ale Akt IV, zaczynający się od Labu 16, już tak.

## Co dalej

Refaktoryzacja zachowała działanie programu, ale nie oznacza to jeszcze,
że program działa poprawnie. Obie te rzeczy trzeba teraz sprawdzać
ręcznie. W następnym labie zautomatyzujesz tę pracę.

Przejdź do [Lab 07 — Skąd wiemy, że to działa?](../07-automated-tests/README.pl.md).
