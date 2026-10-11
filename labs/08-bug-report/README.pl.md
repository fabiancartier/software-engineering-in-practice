# Lab 08 — Nadchodzi zgłoszenie błędu

## Sytuacja

Przychodzi wiadomość od klienta. „Zamówienie miało wartość $60 i
otrzymało rabat lojalnościowy, ale podatek na rachunku jest za wysoki
dla kwoty po rabacie”. Wszystkie testy przechodzą, a klient ma rację.

## Cele nauki

Po tym labie potrafisz:

- Zamienić zgłoszenie błędu w konkretny test, który najpierw nie
  przechodzi, zanim zmienisz implementację.
- Wyjaśnić, dlaczego taki test lepiej potwierdza zrozumienie błędu niż
  dodatkowy `print`.
- Naprawić błąd najmniejszą możliwą zmianą i doprowadzić test od wyniku
  negatywnego do pozytywnego.

## Zanim zaczniesz

- Lab 07 ukończony w wybranej ścieżce, z co najmniej sześcioma
  przechodzącymi testami.

### Python

- Pracuj w `examples/restaurant-bill/python/`.

### Go

- Pracuj w `examples/restaurant-bill/go/`.

### Java

- Pracuj w `examples/restaurant-bill/java/`.

## Twoje zadanie

Każda ścieżka odtwarza i naprawia ten sam błąd na tych samych danych.

1. Oblicz ręcznie lub w konsoli swojego języka wynik całego rachunku
   dla zamówienia, którego suma częściowa
   to $60 (na przykład dwa steki po $30.00) ze stawką napiwku 15%.
   Sprawdź, jaki podatek *powinien* być, jeśli jest liczony od
   kwoty po rabacie ($60 - 10% = $54; 8% z $54 = $4.32), w porównaniu z
   wynikiem obecnego kodu.
2. Dodaj test sprawdzający, że dla zamówienia o wartości $60 i napiwku
   15% zachodzi `tax == 4.32` oraz `total == 66.42`.
3. Uruchom zestaw testów i potwierdź, że nowy test nie przechodzi.
4. Przeczytaj komunikat błędu i znajdź linię obliczającą podatek w
   funkcji liczącej cały rachunek.
5. Przekaż do obliczenia podatku kwotę *po* rabacie zamiast pierwotnej
   sumy częściowej.
6. Ponownie uruchom cały zestaw i potwierdź, że wszystkie testy
   przechodzą, również te z Labu 07.

### Python

Nazwij nowy test
`test_calculate_bill_applies_tax_after_discount_on_large_order`, w
`tests/test_calculator.py`.

### Go

Nazwij nowy test
`TestCalculateBillAppliesTaxAfterDiscountOnLargeOrder`, w
`billing/calculator_test.go`.

### Java

Nazwij nowy test
`calculateBillAppliesTaxAfterDiscountOnLargeOrder`, w
`src/test/java/billing/CalculatorTest.java`.

## Kryteria akceptacji

- Istnieje jasno nazwany test dla zamówienia o wartości $60, który
  sprawdza podatek i kwotę końcową.
- Cały zestaw przechodzi i zawiera co najmniej tyle samo testów co
  wcześniej.
- Poprawka zmienia tylko to, jak podatek jest liczony wewnątrz
  obliczenia całego rachunku. Zachowanie pozostałych funkcji i metod
  pozostaje bez zmian.

## Weryfikacja

### Python

```bash
cd examples/restaurant-bill/python
uv run pytest -v
uv run python -c "from billing.calculator import calculate_bill; print(calculate_bill([('Steak', 30.00, 2)], 0.15))"
cd -
```

Oczekiwany wynik: wszystkie testy mają status `PASSED`, a wyświetlony
słownik zawiera `'tax': 4.32, 'total': 66.42`.

### Go

```bash
cd examples/restaurant-bill/go
go test ./... -v
cd -
```

Oczekiwany wynik: wszystkie testy, w tym nowy, mają status `--- PASS`.

### Java

```bash
cd examples/restaurant-bill/java
./gradlew test
cd -
```

Oczekiwany wynik: `BUILD SUCCESSFUL`; wszystkie testy, w tym nowy,
przechodzą.

## Zastanów się

- Wszystkie testy z Labu 07 przechodziły *przed* tą poprawką, a błąd
  nadal istniał. Co sprawiło, że był niewidoczny
  dla tego zestawu testów?
- Naprawiłeś/aś błąd w obliczeniu całego rachunku, nie w samym
  obliczeniu podatku. Czemu obliczenie podatku nie musiało się zmienić?

## Jeśli utkniesz

### Python

- **Podpowiedź 1:** Najpierw ręcznie oblicz nieprawidłowy podatek. Dla
  zamówienia o wartości $60 porównaj `calculate_tax(60.0)` z
  `calculate_tax(60.0 - 6.0)`.
- **Podpowiedź 2:** Komunikat z pytest pokazuje wartość zwróconą przez
  kod. Porównanie jej z wartością oczekiwaną wskaże błędny argument.
- **Podpowiedź 3:** Poprawka to jeden zmieniony argument na jednej
  linii w `calculate_bill`. Nie przebudowuj pozostałego kodu.

### Go

- **Podpowiedź 1:** Najpierw ręcznie oblicz nieprawidłowy podatek. Dla
  zamówienia o wartości $60 porównaj `CalculateTax(60.0)` z
  `CalculateTax(60.0 - 6.0)`.
- **Podpowiedź 2:** Wynik `t.Errorf` pokazuje wartość zwróconą przez
  kod. Porównanie jej z wartością oczekiwaną wskaże błędny argument.
- **Podpowiedź 3:** Poprawka to jeden zmieniony argument na jednej
  linii w `CalculateBill`. Nie przebudowuj pozostałego kodu.

### Java

- **Podpowiedź 1:** Najpierw ręcznie oblicz nieprawidłowy podatek. Dla
  zamówienia o wartości $60 porównaj `Calculator.calculateTax(60.0)` z
  `Calculator.calculateTax(60.0 - 6.0)`.
- **Podpowiedź 2:** JUnit 5 pokazuje wartość zwróconą przez kod obok
  oczekiwanej. Różnica wskaże błędny argument.
- **Podpowiedź 3:** Poprawka to jeden zmieniony argument na jednej
  linii w `calculateBill`. Nie przebudowuj pozostałego kodu.

Przed przejściem dalej zacommituj i wypchnij wszystkie zmiany poleceniem
`git add -A && git commit -m "..."; git push`. Kolejne laby nie wymagają
jeszcze czystego drzewa, ale Akt IV, zaczynający się od Labu 16, już tak.

## Co dalej

Masz przechodzący zestaw testów i sprawdzoną poprawkę. W następnym labie
dodasz inny rodzaj kontroli: zgodność kodu z zasadami przyjętymi przez
zespół.

Przejdź do [Lab 09 — Maszyny mogą sprawdzać nudne rzeczy](../09-automated-checks/README.pl.md).
