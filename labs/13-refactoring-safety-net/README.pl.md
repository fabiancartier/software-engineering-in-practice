# Lab 13 — Refaktoryzacja z siatką bezpieczeństwa

## Sytuacja

Wersja A działa i ma testy. Ma też coraz dłuższy łańcuch warunków w
funkcji obliczającej rachunek, choć warunki te nie dotyczą sumy
częściowej, podatku ani napiwku. Zmienisz *strukturę* kodu bez zmiany
jego działania, a testy pokażą, czy refaktoryzacja się udała.

## Cele nauki

Po tym labie potrafisz:

- Wprowadzać zmianę strukturalną małymi krokami i po każdym uruchamiać
  testy.
- Wyjaśnić, na czym polega refaktoryzacja bez zmiany zachowania
  (behavior-preserving refactoring).
- Potraktować zaliczone testy jako dowód, że refaktoryzacja niczego nie
  zepsuła, zamiast ponownie czytać całą funkcję.

## Zanim zaczniesz

- Najpierw ukończ lab 12. Twoja kopia wersji A powinna obsługiwać
  `SAVE10`, `SAVE5` i `SAVE20`, a wszystkie testy powinny przechodzić.

### Python

- Pracuj w `examples/discount-codes/version-a/python/`.

### Go

- Pracuj w `examples/discount-codes/version-a/go/`.

### Java

- Pracuj w `examples/discount-codes/version-a/java/`.

## Twoje zadanie

Zrefaktoryzuj wersję A tak, aby obsługiwała kody rabatowe podobnie jak
wersja B. Po każdym zakończonym kroku testy powinny przechodzić.
Wszystkie trzy ścieżki korzystają z tej samej sekwencji.

```text
tests green
→ add new discount-code component
→ existing tests green
→ route behavior through new component
→ tests green
→ remove old conditional logic
→ tests green
```

### Python

1. Utwórz `billing/discount_codes.py` ze słownikiem `DISCOUNT_CODES`.
   Przypisz w nim kody `"SAVE10"`, `"SAVE5"` i `"SAVE20"` do funkcji
   obliczających rabat od podanej kwoty. Rabaty procentowe zapisz jako
   lambdy, a stałe `$5` jako lambdę ignorującą argument. Dodaj też
   funkcję `apply_discount_code(amount, code)`, która wyszukuje kod i
   zgłasza `ValueError`, gdy go nie rozpozna. Skopiuj zachowanie wersji B.
2. Uruchom pełny zestaw testów. Nadal powinien przechodzić, ponieważ na
   razie tylko *dodajesz* plik. Nic w `calculator.py` jeszcze go nie
   wywołuje.
3. W `calculator.py` zamień łańcuch `if/elif/else` wewnątrz
   `calculate_bill` na jedno wywołanie `apply_discount_code`, tylko
   gdy `discount_code is not None`.
4. Od razu ponownie uruchom testy. Jeśli nie przechodzą, zmieniło się
   zachowanie, a nie tylko struktura. Napraw je przed kolejnym krokiem.
5. Usuń z kalkulatora pozostałości starej logiki. Na koniec ponownie
   uruchom testy.

### Go

1. Utwórz `billing/discount_codes.go` z mapą `discountCodes`, która
   przypisuje kody `"SAVE10"`, `"SAVE5"` i `"SAVE20"` do funkcji
   obliczających rabat od podanej kwoty. Dodaj funkcję
   `ApplyDiscountCode(amount, code) (float64, error)`, która wyszukuje
   kod i zwraca błąd, gdy go nie rozpozna. Skopiuj zachowanie wersji B.
2. Uruchom pełny zestaw testów (`go test ./...`). Nadal powinien
   przechodzić. Go pozwala na nieużywaną mapę lub funkcję na poziomie
   pakietu, a `calculator.go` jeszcze nie wywołuje nowego kodu.
3. W `calculator.go` zamień `switch` wewnątrz `CalculateBill` na jedno
   wywołanie `ApplyDiscountCode`, tylko gdy `discountCode != ""`.
4. Od razu ponownie uruchom testy. Jeśli nie przechodzą, zmieniło się
   zachowanie, a nie tylko struktura. Napraw je przed kolejnym krokiem.
5. Usuń z kalkulatora pozostałości starej logiki. Sprawdź, czy
   `calculator.go` nadal potrzebuje importu `"fmt"`. Gdy `switch`,
   który budował komunikat błędu przez `fmt.Errorf`, już nie istnieje,
   import staje się nieużywany, a Go przerywa kompilację. Przed
   ponownym uruchomieniem testów sprawdź to poleceniem `go build ./...`.

### Java

1. Utwórz `src/main/java/billing/DiscountCodes.java` z mapą `CODES`
   (`Map<String, DoubleUnaryOperator>`), która przypisuje `"SAVE10"`,
   `"SAVE5"` i `"SAVE20"` do funkcji obliczających rabat. Dodaj metodę
   `apply(double amount, String code)`, która wyszukuje kod i zgłasza
   `IllegalArgumentException`, gdy go nie rozpozna. Skopiuj zachowanie
   wersji B.
2. Uruchom pełny zestaw testów (`./gradlew test`). Nadal powinien
   przechodzić. Nieużywana klasa nie przeszkadza w kompilacji Javy, a
   `Calculator.java` jeszcze jej nie wywołuje.
3. W `Calculator.java` zamień `switch` wewnątrz `calculateBill` na
   jedno wywołanie `DiscountCodes.apply`, tylko gdy `discountCode !=
   null`.
4. Od razu ponownie uruchom testy. Jeśli nie przechodzą, zmieniło się
   zachowanie, a nie tylko struktura. Napraw je przed kolejnym krokiem.
5. Usuń z kalkulatora pozostałości starej logiki. Na koniec ponownie
   uruchom testy.

## Kryteria akceptacji

- Plik kodów rabatowych (`discount_codes.py`, `discount_codes.go` albo
  `DiscountCodes.java`) zawiera te same trzy kody co wersja B.
- Obliczenie rachunku nie zawiera już łańcucha warunków, który
  bezpośrednio sprawdza napisy z kodami rabatowymi.
- Zestaw testów przechodzi na każdym opisanym powyżej etapie, nie
  tylko na końcu.

## Weryfikacja

### Python

```bash
cd examples/discount-codes/version-a/python
uv run pytest -v
grep -n "elif discount_code" billing/calculator.py && echo "still coupled — not done" || echo "decoupled"
cd -
```

Wszystkie testy powinny przejść, a polecenie powinno wypisać
`decoupled`.

### Go

```bash
cd examples/discount-codes/version-a/go
go build ./...
go test ./... -v
grep -n 'case "SAVE' billing/calculator.go && echo "still coupled — not done" || echo "decoupled"
cd -
```

Kompilacja i testy powinny się udać, a polecenie powinno wypisać
`decoupled`.

### Java

```bash
cd examples/discount-codes/version-a/java
./gradlew test
grep -n "switch (discountCode)" src/main/java/billing/Calculator.java && echo "still coupled — not done" || echo "decoupled"
cd -
```

Kompilacja i testy powinny się udać, a polecenie powinno wypisać
`decoupled`.

## Zastanów się

- Na którym kroku testy natychmiast wykryłyby literówkę? Który krok
  mógłby wprowadzić zmianę zachowania niewykrywaną przez obecne testy?
- Wersja A ma teraz taką samą strukturę jak wersja B. Co na każdym
  etapie stanowiło *dowód*, że działanie programu się nie zmieniło?

## Jeśli utkniesz

### Python

- **Podpowiedź 1.** W krokach 1-2 tylko dodajesz kod, więc dotychczasowe
  działanie nie może się jeszcze zepsuć. To celowe. Testy regresji nadal
  chronią starą implementację, a dopiero następny krok skieruje ją do
  nowego komponentu.
- **Podpowiedź 2.** W kroku 3 zastąp cały blok
  `if discount_code == "SAVE10": ... elif ...: ... else: raise ...`
  przez `code_discount = apply_discount_code(after_loyalty,
  discount_code)`.
- **Podpowiedź 3.** Jeśli po kroku 3 test nie przechodzi, porównaj
  działanie `apply_discount_code` dla tego kodu z działaniem starej
  gałęzi. Różnica zwykle dotyczy jednego z trzech kodów.

### Go

- **Podpowiedź 1.** W krokach 1-2 tylko dodajesz kod, więc dotychczasowe
  działanie nie może się jeszcze zepsuć. W
  przeciwieństwie do nieużywanej zmiennej *lokalnej*, nieużywana
  funkcja albo mapa na poziomie pakietu nie jest błędem kompilacji w
  Go.
- **Podpowiedź 2.** W kroku 3 zastąp cały blok
  `switch discountCode { case "SAVE10": ... default: return Bill{},
  fmt.Errorf(...) }` przez `codeDiscount, err =
  ApplyDiscountCode(afterLoyalty, discountCode)` i dodaj sprawdzenie
  `if err != nil`.
- **Podpowiedź 3.** Po kroku 3 polecenie `go build ./...` może zgłosić
  `"fmt" imported and not used`. To oczekiwane, ponieważ jedynym kodem,
  który używał `fmt`, był komunikat błędu wewnątrz starego `switch`.
  Usuń import albo pozostaw samo `import "math"`, jeśli plik nadal
  potrzebuje tylko tego pakietu.

### Java

- **Podpowiedź 1.** W krokach 1-2 tylko dodajesz kod, więc dotychczasowe
  działanie nie może się jeszcze zepsuć.
- **Podpowiedź 2.** W kroku 3 zastąp cały blok
  `switch (discountCode) { case "SAVE10": ... default: throw ... }`
  przez `codeDiscount = DiscountCodes.apply(afterLoyalty,
  discountCode);`.
- **Podpowiedź 3.** Jeśli po kroku 3 test nie przechodzi, porównaj
  działanie `DiscountCodes.apply` dla tego kodu z działaniem starego
  `case`. Różnica zwykle dotyczy jednego z trzech kodów.

Przed przejściem dalej utwórz commit ze wszystkim z tego laba i
wypchnij go do repozytorium
(`git add -A && git commit -m "..."; git push`). Nic później jeszcze
nie wymaga czystego drzewa roboczego, ale Akt IV (od labu 16) już tak.
Warto od razu wyrobić sobie ten nawyk.

## Co dalej

Ostatnie dwa laby pokazały, że kody rabatowe tworzą rodzinę zachowań
wybieranych na podstawie klucza. W kolejnym labie zobaczysz tę samą
strukturę w zupełnie innej części systemu, a potem poznasz jej nazwę.

Przejdź do [Lab 14 — Jeden kontrakt, trzy języki](../14-one-contract-three-languages/README.pl.md).
