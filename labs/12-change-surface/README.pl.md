# Lab 12 — Gdzie powinna trafić ta zmiana?

## Sytuacja

Dwóch programistów niezależnie zaimplementowało kody rabatowe opisane
w labie 11. Obie wersje zachowują się dziś identycznie, ale ich
rozbudowa wymaga różnego nakładu pracy.

Ten lab i trzy kolejne wyglądają tak samo w każdym języku. Gdy
instrukcja dzieli się na ścieżki, czytaj tylko tę wybraną w Akcie II.

## Cele nauki

Po tym labie potrafisz:

- Wskazać, które pliki trzeba zmienić przy dodawaniu wymagania do danego
  projektu.
- Wyjaśnić „sprzężenie” (coupling) i „spójność” (cohesion), używając
  konkretnego przykładu, a nie definicji.
- Oceniać projekt na podstawie kosztu zmian, a nie tylko tego, czy obecnie
  działa.

## Zanim zaczniesz

- Laby 06-11 ukończone w wybranej ścieżce.
- Przeczytaj oba `version-a/<twój-język>/` i `version-b/<twój-język>/`,
  zanim zrobisz cokolwiek innego. Dokładne pliki znajdziesz poniżej.
  Sprawdź, czy obie wersje przechodzą testy i wyliczają te same kwoty.

### Python

- `examples/discount-codes/version-a/python/billing/calculator.py` i
  `examples/discount-codes/version-b/python/billing/calculator.py`
  (oraz `billing/discount_codes.py` wersji B).

### Go

- `examples/discount-codes/version-a/go/billing/calculator.go` i
  `examples/discount-codes/version-b/go/billing/calculator.go` (oraz
  `billing/discount_codes.go` wersji B).

### Java

- `examples/discount-codes/version-a/java/src/main/java/billing/Calculator.java`
  i
  `examples/discount-codes/version-b/java/src/main/java/billing/Calculator.java`
  (oraz `billing/DiscountCodes.java` wersji B).

## Twoje zadanie

Właściciel dodał trzeci kod, `SAVE20`. Obniża on o 20% kwotę pozostałą
po rabacie lojalnościowym, czyli działa tak samo jak `SAVE10`, ale ma
inną wartość. Dodaj go do **obu** wersji w swoim języku.

Do sprawdzenia dużego zamówienia z dwoma stekami po $30.00 i napiwkiem
15% użyj następujących kwot. Suma częściowa to `$60.00`, rabat
lojalnościowy `$6.00`, rabat `SAVE20` `$10.80`, łączny rabat `$16.80`,
a oczekiwana kwota do zapłaty `$53.14`.

### Python

1. Dodaj obsługę `SAVE20` do **wersji A**
   (`examples/discount-codes/version-a/python/`). Dodaj test w
   `tests/test_calculator.py`, który dla dużego zamówienia sprawdza, że
   `bill["discount"] == 16.8` i `bill["total"] == 53.14`.
2. Dodaj obsługę `SAVE20` do **wersji B**
   (`examples/discount-codes/version-b/python/`). Dodaj tam też
   analogiczny test.

### Go

1. Dodaj gałąź `case "SAVE20":` do instrukcji `switch` w `CalculateBill`
   w **wersji A** (`examples/discount-codes/version-a/go/`) i oblicz w
   niej 20% kwoty po rabacie lojalnościowym. Dodaj
   `TestSave20AppliesAfterLoyaltyDiscount` do `calculator_test.go`,
   który sprawdza `bill.Discount == 16.8` i `bill.Total == 53.14` dla
   dużego zamówienia.
2. Dodaj wpis `"SAVE20"` do mapy `discountCodes` w
   `discount_codes.go` w **wersji B**
   (`examples/discount-codes/version-b/go/`). Dodaj tam też
   analogiczny test do `calculator_test.go`.

### Java

1. Dodaj gałąź `case "SAVE20":` do instrukcji `switch` w `calculateBill`
   w **wersji A** (`examples/discount-codes/version-a/java/`) i oblicz
   w niej 20% kwoty po rabacie lojalnościowym. Dodaj
   `save20AppliesAfterLoyaltyDiscount` do `CalculatorTest.java`,
   który sprawdza `bill.discount == 16.8` i `bill.total == 53.14` dla
   dużego zamówienia.
2. Dodaj wpis `"SAVE20"` do mapy `CODES` w `DiscountCodes.java` w
   **wersji B** (`examples/discount-codes/version-b/java/`). Dodaj
   tam też analogiczny test do `CalculatorTest.java`.

## Wszystkie ścieżki

3. Dla każdej wersji zapisz, które pliki trzeba było zmienić. Jaki
   *inny* kod znajduje się obok Twojej zmiany? Poszukaj kodu
   odpowiedzialnego za coś niezwiązanego z kodami rabatowymi.
4. Zapisz odpowiedź w `examples/discount-codes/COMPARISON.md`. Wyobraź
   sobie, że tuż po Twojej zmianie pojawia się błąd w obliczaniu podatku.
   W której wersji łatwiej wykluczyć zmianę kodu rabatowego jako
   przyczynę, patrząc tylko na *miejsce* jej wprowadzenia? Wszystkie
   ścieżki językowe korzystają z tego samego pliku, więc opisz w nim
   tylko swoją ścieżkę.

## Kryteria akceptacji

- W obu wersjach przechodzą wszystkie testy, łącznie z nowymi testami
  `SAVE20`.
- `COMPARISON.md` zawiera nazwę pliku zmienionego w każdej wersji i
  odpowiada na pytanie z kroku 4.

### Python

- W wersji A powinno przejść 5 testów, a w wersji B 8.

### Go

- W wersji A powinno przejść 5 testów (4 istniejące funkcje `Test...`
  oraz nowa), a w wersji B 8 (7 istniejących oraz nowa).

### Java

- W wersji A powinno przejść 5 testów, a w wersji B 8.

## Weryfikacja

### Python

```bash
cd examples/discount-codes/version-a/python && uv run pytest -v && cd - > /dev/null
cd examples/discount-codes/version-b/python && uv run pytest -v && cd - > /dev/null
test -f examples/discount-codes/COMPARISON.md && echo "comparison notes exist"
```

Oczekiwany wynik to 5 zaliczonych testów w wersji A, 8 w wersji B oraz
istniejący plik z notatkami porównawczymi.

### Go

```bash
cd examples/discount-codes/version-a/go && go test ./... -v && cd - > /dev/null
cd examples/discount-codes/version-b/go && go test ./... -v && cd - > /dev/null
test -f examples/discount-codes/COMPARISON.md && echo "comparison notes exist"
```

Oczekiwany wynik to 5 zaliczonych testów w wersji A, 8 w wersji B oraz
istniejący plik z notatkami porównawczymi.

### Java

```bash
cd examples/discount-codes/version-a/java && ./gradlew test && cd - > /dev/null
cd examples/discount-codes/version-b/java && ./gradlew test && cd - > /dev/null
test -f examples/discount-codes/COMPARISON.md && echo "comparison notes exist"
```

Oczekiwany wynik to 5 zaliczonych testów w wersji A, 8 w wersji B oraz
istniejący plik z notatkami porównawczymi.

## Zastanów się

- Obie wersje wymagały od Ciebie zmiany dokładnie jednego pliku. Czy
  „ta sama liczba zmienionych plików” oznacza „ten sam koszt zmiany”?
  Czym właściwie różnią się te dwa pliki?
- Czy w wersji B da się dodać czwarty kod rabatowy bez czytania ani
  jednej linii pliku kalkulatora? Co mówi to o sprzężeniu pliku z kodami
  rabatowymi z resztą logiki rozliczeń?

## Jeśli utkniesz

### Python

- **Podpowiedź 1.** W wersji A dodajesz nową gałąź `elif` w
  `calculate_bill`. W wersji B dodajesz nowy wpis do słownika
  `DISCOUNT_CODES` w `discount_codes.py`.
- **Podpowiedź 2.** „20% zniżki od kwoty pozostałej po rabacie
  lojalnościowym” działa tak samo jak `SAVE10`, ale korzysta z innej
  stawki.
- **Podpowiedź 3.** W dużym zamówieniu suma częściowa wynosi $60, rabat
  lojalnościowy $6, a napiwek 15%. Po rabacie lojalnościowym pozostają
  $54, więc `SAVE20` daje $10.80 rabatu. Łączny rabat wynosi $16.80.

### Go

- **Podpowiedź 1.** W wersji A dodajesz nowy `case` do instrukcji
  `switch` w `CalculateBill`. W wersji B dodajesz nowy wpis do mapy
  `discountCodes` w `discount_codes.go`.
- **Podpowiedź 2.** „20% zniżki od kwoty pozostałej po rabacie
  lojalnościowym” działa tak samo jak `SAVE10`, ale korzysta z innej
  stawki. Użyj istniejącej gałęzi lub wpisu mapy jako wzoru.
- **Podpowiedź 3.** W dużym zamówieniu suma częściowa wynosi $60, rabat
  lojalnościowy $6, a napiwek 15%. Po rabacie lojalnościowym pozostają
  $54, więc `SAVE20` daje $10.80 rabatu. Łączny rabat wynosi $16.80.

### Java

- **Podpowiedź 1.** W wersji A dodajesz nowy `case` do instrukcji
  `switch` w `calculateBill`. W wersji B dodajesz nowy wpis do mapy
  `CODES` w `DiscountCodes.java`.
- **Podpowiedź 2.** „20% zniżki od kwoty pozostałej po rabacie
  lojalnościowym” działa tak samo jak `SAVE10`, ale korzysta z innej
  stawki. Użyj istniejącej gałęzi lub wpisu mapy jako wzoru.
- **Podpowiedź 3.** W dużym zamówieniu suma częściowa wynosi $60, rabat
  lojalnościowy $6, a napiwek 15%. Po rabacie lojalnościowym pozostają
  $54, więc `SAVE20` daje $10.80 rabatu. Łączny rabat wynosi $16.80.

Przed przejściem dalej utwórz commit ze wszystkim z tego laba i
wypchnij go do repozytorium
(`git add -A && git commit -m "..."; git push`). Nic później jeszcze
nie wymaga czystego drzewa roboczego, ale Akt IV (od labu 16) już tak.
Warto od razu wyrobić sobie ten nawyk.

## Co dalej

Widać już różnicę między projektem, w którym nowe wymaganie jest tanie
we wdrożeniu, a takim, który jedynie pozwala je wdrożyć. Wersja A nadal
ma silnie sprzężoną strukturę, ale ma też testy. W następnym labie
zbliżysz ją do wersji B, nie psując po drodze jej działania.

Przejdź do [Lab 13 — Refaktoryzacja z siatką bezpieczeństwa](../13-refactoring-safety-net/README.pl.md).
