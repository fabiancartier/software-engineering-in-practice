# Lab 07 — Skąd wiemy, że to działa?

## Sytuacja

Po refaktoryzacji kod `billing` nadal działa tak samo jak pierwotny
monolit. Sprawdziłeś/aś to ręcznie za pomocą `diff`, ale powtarzanie
takiej kontroli po każdej zmianie byłoby uciążliwe. Czas ją
zautomatyzować.

## Cele nauki

Po tym labie potrafisz:

- Napisać test jednostkowy według schematu Arrange-Act-Assert.
- Wyjaśnić, czym jest "jednostka" w "teście jednostkowym", w kontekście
  tego projektu.
- Wybrać przypadki testowe, które obejmują różne zachowania funkcji lub
  metody, a nie tylko scenariusz bez błędów.
- Uruchomić zestaw testów i odczytać jego wynik za pomocą narzędzi
  dostępnych w wybranym języku.

## Zanim zaczniesz

- Ukończony Lab 06 w wybranej ścieżce językowej.

### Python

- Bieżący katalog: `examples/restaurant-bill/python/`.
- Plik `billing/calculator.py` zawiera pięć funkcji utworzonych w
  Labie 06.

### Go

- Bieżący katalog: `examples/restaurant-bill/go/`.
- Plik `billing/calculator.go` zawiera typy `Item` i `Bill` oraz pięć
  funkcji utworzonych w Labie 06.

### Java

- Bieżący katalog: `examples/restaurant-bill/java/`.
- Plik `billing/Calculator.java` zawiera typy `Item` i `Bill` oraz pięć
  metod utworzonych w Labie 06. W `build.gradle` są już zależności
  JUnit 5.

## Twoje zadanie

W każdej ścieżce napisz testy dla sześciu poniższych przypadków. Ułóż
każdy test według schematu Arrange (przygotuj dane), Act (wywołaj
funkcję), Assert (sprawdź wynik). Każda z tych części może zajmować
tylko jedną linię.

1. Suma częściowa uwzględnia `price * quantity` dla wielu pozycji.
2. Rabat wynosi `0`, gdy suma częściowa jest mniejsza niż `50`.
3. Rabat wynosi 10% sumy częściowej równej co najmniej `50`.
4. Podatek wynosi 8% przekazanej kwoty.
5. Napiwek stanowi podany procent przekazanej kwoty.
6. Pełny rachunek dla **małego zamówienia bez rabatu** zwraca kwotę
   `46.74`. Użyj tych samych produktów co w przykładzie: burgera,
   frytek i napoju o łącznej wartości $38.

### Python

Utwórz `tests/test_calculator.py`. Poniższy przykład pokazuje pierwszy
przypadek. Pozostałe pięć napisz samodzielnie w podobnej formie.

```python
from billing.calculator import calculate_subtotal


def test_calculate_subtotal_sums_multiple_items():
    items = [("Burger", 12.50, 2), ("Fries", 4.00, 2)]

    result = calculate_subtotal(items)

    assert result == 33.00
```

### Go

Utwórz `billing/calculator_test.go` w tym samym pakiecie co
`calculator.go`. Dzięki temu testy mają bezpośredni dostęp do jego
zawartości. Poniżej znajdziesz przykład pierwszego przypadku;
pozostałe pięć napisz samodzielnie. Przypadki 2 i 3 możesz połączyć w
test tabelaryczny: przygotuj `[]struct{...}` z danymi wejściowymi i
oczekiwanymi wynikami, a następnie wykonaj przypadki za pomocą
`t.Run`.

```go
package billing

import "testing"

func TestCalculateSubtotalSumsMultipleItems(t *testing.T) {
	items := []Item{
		{Name: "Burger", Price: 12.50, Qty: 2},
		{Name: "Fries", Price: 4.00, Qty: 2},
	}

	got := CalculateSubtotal(items)

	want := 33.00
	if got != want {
		t.Errorf("got %.2f, want %.2f", got, want)
	}
}
```

### Java

Utwórz `src/test/java/billing/CalculatorTest.java` w pakiecie
`billing`, tak jak `Calculator.java`. Poniższy przykład pokazuje
pierwszy przypadek. Pozostałe pięć napisz samodzielnie w podobnej
formie.

```java
package billing;

import static org.junit.jupiter.api.Assertions.assertEquals;

import java.util.List;
import org.junit.jupiter.api.Test;

class CalculatorTest {

    @Test
    void calculateSubtotalSumsMultipleItems() {
        List<Calculator.Item> items = List.of(
                new Calculator.Item("Burger", 12.50, 2),
                new Calculator.Item("Fries", 4.00, 2));

        double subtotal = Calculator.calculateSubtotal(items);

        assertEquals(33.00, subtotal);
    }
}
```

## Kryteria akceptacji

- Istnieje co najmniej sześć testów, po jednym dla każdego przypadku.
  Możesz napisać ich więcej, na przykład rozdzielając dwa warianty
  rabatu.
- Każdy test zachowuje układ Arrange-Act-Assert. Nie dodawaj
  dodatkowych bibliotek ani konfiguracji ponad to, co zapewnia
  standardowe narzędzie wybranego języka.

### Python

- `uv run pytest -v` przechodzi, z co najmniej jednym testem na każdą
  funkcję wymienioną powyżej.

### Go

- `go test ./...` przechodzi, z co najmniej jednym testem (albo
  subtestem, jeśli użyłeś/aś testów table-driven) na każdy przypadek
  wymieniony powyżej.

### Java

- `./gradlew test` przechodzi, z co najmniej jednym testem na każdy
  przypadek wymieniony powyżej.

## Weryfikacja

### Python

```bash
cd examples/restaurant-bill/python
uv run pytest -v
cd -
```

Wszystkie testy powinny mieć wynik `PASSED`. Żaden nie powinien być
pominięty ani zakończony wynikiem `FAILED`.

### Go

```bash
cd examples/restaurant-bill/go
go test ./... -v
cd -
```

Każdy test powinien mieć wynik `--- PASS`, a żaden `--- FAIL`.

### Java

```bash
cd examples/restaurant-bill/java
./gradlew test
cd -
```

Oczekiwany wynik to `BUILD SUCCESSFUL`. Otwórz
`build/reports/tests/test/index.html` w przeglądarce, jeśli chcesz
zobaczyć każdy test wylistowany osobno.

## Zastanów się

- Wszystkie sześć Twoich testów przechodzi. Czy to dowodzi, że
  obliczenie całego rachunku jest poprawne dla *każdego* zamówienia,
  czy tylko dla konkretnych wejść, które wypróbowałeś/aś?
- Osobno przetestowałeś/aś małe zamówienie i rabat dla dużej wartości.
  Czy któryś test oblicza jednak cały rachunek dla zamówienia objętego
  rabatem? Jaki błąd mógłby ujawnić taki przypadek?

## Jeśli utkniesz

### Python

- **Podpowiedź 1:** Zaimportuj to, co testujesz, na górze pliku:
  `from billing.calculator import calculate_subtotal, calculate_discount,
  calculate_tax, calculate_tip, calculate_bill`.
- **Podpowiedź 2:** Test jest funkcją, której nazwa zaczyna się od
  `test_` i która zawiera instrukcje `assert`. Pytest znajdzie ją i
  uruchomi automatycznie.
- **Podpowiedź 3:** Wyniki zmiennoprzecinkowe możesz porównywać przez
  `==` po zaokrągleniu do 2 miejsc po przecinku (co `calculate_bill`
  już robi). W tym projekcie nie potrzebujesz `pytest.approx`.

### Go

- **Podpowiedź 1:** Plik testowy w `package billing` zamiast
  `package billing_test` ma dostęp także do nieeksportowanych elementów
  pakietu. W tym labie wystarczy jednak pięć eksportowanych funkcji.
- **Podpowiedź 2:** `go test` automatycznie uruchamia funkcje o nazwie
  `TestXxx(t *testing.T)` zapisane w plikach zakończonych na
  `_test.go`.
- **Podpowiedź 3:** W teście tabelarycznym zapisz nazwę, dane wejściowe
  i oczekiwany wynik w strukturach, a potem wykonaj je w pętli z
  `t.Run`. Unikniesz w ten sposób kopiowania asercji.

### Java

- **Podpowiedź 1:** JUnit 5 automatycznie wykrywa metody oznaczone
  `@Test`, które nie przyjmują argumentów i zwracają `void`.
- **Podpowiedź 2:** W `assertEquals(expected, actual)` najpierw podaj
  wartość oczekiwaną. Odwrotna kolejność nie zepsuje testu, ale utrudni
  czytanie komunikatu o błędzie.
- **Podpowiedź 3:** `assertEquals(double, double)` porównuje wartości
  bez tolerancji. Tutaj to wystarczy, ponieważ obliczane wartości są
  już zaokrąglane do dwóch miejsc po przecinku.

Przed przejściem dalej zacommituj i wypchnij zmiany z tego laba:
`git add -A && git commit -m "..."; git push`. Czyste drzewo robocze
będzie wymagane od Labu 16, więc warto już teraz wyrobić sobie ten
nawyk.

## Co dalej

Wszystkie testy przechodzą, a mimo to klient zgłasza błąd na rachunku.
W następnym labie sprawdzisz, dlaczego zielony zestaw testów nie zawsze
oznacza poprawny program.

Przejdź do [Lab 08 — Nadchodzi zgłoszenie błędu](../08-bug-report/README.pl.md).
