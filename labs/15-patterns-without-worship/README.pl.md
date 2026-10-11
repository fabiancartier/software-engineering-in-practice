# Lab 15 — Wzorce bez bałwochwalstwa

## Sytuacja

Wróć do pliku kodów rabatowych z wersji B i implementacji `Notifier` z
labu 14 w wybranej ścieżce. Oba rozwiązania powstały, zanim pojawiła
się ich „oficjalna” nazwa. Teraz czas ją poznać.

## Cele nauki

Po tym labie potrafisz:

- Rozpoznać wzorzec Strategy w istniejącym kodzie, zanim poznasz jego
  nazwę.
- Wyjaśnić Dependency Injection na przykładzie funkcji
  `send_receipt_ready`, `SendReceiptReady` albo `sendReceiptReady`,
  zamiast powtarzać definicję.
- Wyjaśnić w jednym zdaniu, do czego służą Factory i Adapter.

## Zanim zaczniesz

- Najpierw ukończ laby 12-14 w wybranej ścieżce.
- Nie potrzebujesz nowych narzędzi. W tym labie wracasz do istniejącego
  kodu.

## Twoje zadanie

1. Przeczytaj ponownie plik kodów rabatowych wersji B i jego funkcję w
   rodzaju `apply` dla wybranej ścieżki. Wróć też do implementacji
   `Notifier` i funkcji w rodzaju `send_receipt_ready` z labu 14. W
   pliku notatek `labs/15-patterns-without-worship/my-notes.md` napisz,
   własnymi słowami, co te dwa fragmenty kodu mają wspólnego. Skup się
   na tym, jak unikają długiego łańcucha warunków przy wyborze
   zachowania.

### Python

- `examples/discount-codes/version-b/python/billing/discount_codes.py`
- `examples/notifier/python/notifier/notifier.py`

### Go

- `examples/discount-codes/version-b/go/billing/discount_codes.go`
- `examples/notifier/go/notifier.go`

### Java

- `examples/discount-codes/version-b/java/src/main/java/billing/DiscountCodes.java`
- `examples/notifier/java/{Notifier,ConsoleNotifier,InMemoryNotifier,ReceiptService}.java`

2. Ta struktura ma nazwę. Kilka wymiennych implementacji tego samego
   niewielkiego kontraktu jest wybieranych przez kod wywołujący, zamiast
   trafiać do jednego dużego warunku. To wzorzec **Strategy**. Każdy
   wpis w mapie lub słowniku kodów rabatowych jest strategią.
   `ConsoleNotifier`, `InMemoryNotifier` i `SilentNotifier` to strategie
   dostarczania powiadomień. Strategy nie wymaga hierarchii klas. W Go
   i Pythonie strategią może być zwykła funkcja przechowywana w mapie
   lub słowniku; nie trzeba opakowywać jej w osobny typ.
3. Funkcja `send_receipt_ready`, `SendReceiptReady` lub
   `sendReceiptReady` otrzymuje strategię jako *parametr*, zamiast
   tworzyć ją wewnętrznie. Nie zawiera więc instrukcji w rodzaju
   `notifier = ConsoleNotifier()`. Przekazanie zależności z zewnątrz
   nazywa się **Dependency Injection**. Nie wymaga frameworka; to kod
   wywołujący wybiera implementację i przekazuje ją jako argument.
   Napisz w notatkach jedno zdanie o tym, czego funkcja nie mogłaby już
   zrobić, gdyby zawsze samodzielnie tworzyła `ConsoleNotifier`.
4. Przydadzą się jeszcze dwie nazwy. **Factory** skupia w jednym miejscu
   decyzję o tym, *który* obiekt utworzyć lub zwrócić, dzięki czemu kod
   wywołujący nie musi za każdym razem podejmować jej samodzielnie.
   Factory może dostarczać dowolny rodzaj obiektu. W tym ćwiczeniu
   wybierałaby akurat implementację Strategy. Przykładem byłaby funkcja
   `build_notifier(config)`, która na podstawie ustawień zwraca
   `ConsoleNotifier` albo `InMemoryNotifier`. Nie musisz jej budować,
   ale potrafisz już rozpoznać jej rolę. Wybór strategii jest tylko
   jednym z zastosowań Factory, a nie jego definicją. **Adapter** opakowuje
   obiekt o niezgodnym interfejsie, aby pasował do kodu, który już masz.
   Jeśli zewnętrzna biblioteka SMS udostępnia `sendMessage(text)` zamiast
   `send(message)`, niewielka klasa tłumacząca jedno wywołanie na drugie
   jest adapterem. Opisz każdy z tych wzorców jednym własnym zdaniem.
5. Dla wprawy dodaj do wersji B w **wybranej ścieżce** jeszcze jeden
   kod rabatowy. `"SAVE_FLAT2"` obniża kwotę o 2 dolary i powinien mieć
   własny test. Zapisz w notatkach, ile linii trzeba było dodać i czy
   trzeba było zmienić istniejącą logikę.

### Python

Dodaj `"SAVE_FLAT2": lambda amount: 2.0` do `DISCOUNT_CODES` w
`examples/discount-codes/version-b/python/billing/discount_codes.py`.
Dodaj test do `tests/test_discount_codes.py`, który sprawdza
`apply_discount_code(100.0, "SAVE_FLAT2") == 2.0`.

### Go

Dodaj `"SAVE_FLAT2": func(amount float64) float64 { return 2.0 }` do
mapy `discountCodes` w
`examples/discount-codes/version-b/go/billing/discount_codes.go`.
Dodaj test do `discount_codes_test.go`, który sprawdza, że
`ApplyDiscountCode(100.0, "SAVE_FLAT2")` zwraca `2.0`. Następnie
uruchom `gofmt -w .`. Dłuższy klucz zmieni wyrównanie kolumny
`:` przy wszystkich wpisach. To oczekiwany efekt formatowania, a nie
oznaka błędu.

### Java

Dodaj `"SAVE_FLAT2", amount -> 2.0` do mapy `CODES` w
`examples/discount-codes/version-b/java/src/main/java/billing/DiscountCodes.java`.
Dodaj test do `DiscountCodesTest.java`, który sprawdza
`DiscountCodes.apply(100.0, "SAVE_FLAT2") == 2.0`.

Nie dodawaj tego kodu do wersji A ani do publicznej wersji startowej.
To ćwiczenie dotyczy tylko Twojej kopii roboczej.

## Kryteria akceptacji

- `my-notes.md` zawiera własne odpowiedzi na punkty 1, 3 i 4, a nie
  tekst skopiowany z tego pliku.
- Wersja B w wybranej ścieżce ma nowy kod rabatowy i zaliczony test, a
  notatki podają liczbę zmienionych linii i plików.

## Weryfikacja

### Python

```bash
test -f labs/15-patterns-without-worship/my-notes.md && echo "notes exist"
cd examples/discount-codes/version-b/python && uv run pytest -v && cd - > /dev/null
```

Plik z notatkami powinien istnieć, a wszystkie 9 testów powinny
przejść. Wersja B początkowo miała 7 testów; kolejny test dla `SAVE20`
doszedł w labie 12, a ostatni dodajesz tutaj.

### Go

```bash
test -f labs/15-patterns-without-worship/my-notes.md && echo "notes exist"
cd examples/discount-codes/version-b/go && go test ./... -v && cd - > /dev/null
```

Plik z notatkami powinien istnieć, a wszystkie 9 testów powinny
przejść. Wersja B początkowo miała 7 testów; kolejny test dla `SAVE20`
doszedł w labie 12, a ostatni dodajesz tutaj.

### Java

```bash
test -f labs/15-patterns-without-worship/my-notes.md && echo "notes exist"
cd examples/discount-codes/version-b/java && ./gradlew test && cd - > /dev/null
```

Plik z notatkami powinien istnieć, a wszystkie 9 testów powinny
przejść. Wersja B początkowo miała 7 testów; kolejny test dla `SAVE20`
doszedł w labie 12, a ostatni dodajesz tutaj.

## Zastanów się

- Strategy, Factory, Adapter i Dependency Injection to cztery różne
  nazwy. Która z nich opisuje *czym jest dany fragment kodu*, a która
  *jak otrzymuje zależności*? Czy mapa lub słownik kodów
  rabatowych jest bliżej jednej czy drugiej?
- Czy znajomość tych nazw wystarczyłaby, aby od razu wybrać Strategy w
  labie 12? A może dopiero praca ze sprzężoną wersją pokazała, co ten
  wzorzec faktycznie daje?

## Jeśli utkniesz

### Python

- **Podpowiedź 1.** Jeśli nie masz pewności, czy kod realizuje Strategy,
  sprawdź, czy da się zastąpić ten fragment inną implementacją tego
  samego kontraktu bez zmiany kodu wywołującego. Jeśli tak, rozpoznajesz
  ten wzorzec.
- **Podpowiedź 2.** Dependency Injection nie oznacza tutaj frameworka.
  Kod wywołujący wybiera implementację i przekazuje ją jako argument.
- **Podpowiedź 3.** Nowy kod rabatowy zbuduj tak samo jak `"SAVE5"` w
  `DISCOUNT_CODES`: lambda ignoruje argument i zwraca stałą kwotę.

### Go

- **Podpowiedź 1.** Jeśli nie masz pewności, czy kod realizuje Strategy,
  sprawdź, czy da się zastąpić ten fragment inną implementacją tego
  samego kontraktu bez zmiany kodu wywołującego. Jeśli tak, rozpoznajesz
  ten wzorzec.
- **Podpowiedź 2.** Dependency Injection nie oznacza tutaj frameworka.
  Kod wywołujący wybiera implementację i przekazuje ją jako argument.
- **Podpowiedź 3.** Nowy kod rabatowy zbuduj tak samo jak `"SAVE5"` w
  mapie `discountCodes`: funkcja ignoruje argument i zwraca stałą kwotę.

### Java

- **Podpowiedź 1.** Jeśli nie masz pewności, czy kod realizuje Strategy,
  sprawdź, czy da się zastąpić ten fragment inną implementacją tego
  samego kontraktu bez zmiany kodu wywołującego. Jeśli tak, rozpoznajesz
  ten wzorzec.
- **Podpowiedź 2.** Dependency Injection nie oznacza tutaj frameworka.
  Kod wywołujący wybiera implementację i przekazuje ją jako argument.
- **Podpowiedź 3.** Nowy kod rabatowy zbuduj tak samo jak `"SAVE5"` w
  mapie `CODES`: lambda ignoruje argument i zwraca stałą kwotę.

## Zanim przejdziesz do Aktu IV

Akt IV (od labu 16) zakłada, że gałąź `main` jest czysta, a wszystko z
labów 06-15 zostało zapisane w commitach i wypchnięte. Sprawdź to teraz.

```bash
git status
```

Jeśli są niezapisane zmiany, utwórz commit i wypchnij go teraz
(`git add -A && git commit -m "..."; git push`). Czyste drzewo robocze
oznacza, że możesz przejść dalej.

## Co dalej

Masz za sobą zbudowanie niewielkiej funkcji, nazwanie zastosowanego
rozwiązania i ponowne użycie go przy nowych wymaganiach. Akt III jest
zakończony. W następnym przestajesz pracować w pojedynkę, a „u mnie
działa” musi zmienić się w „działa także wtedy, gdy kod zmienia ktoś
inny”.

Przejdź do [Lab 16 — Gałęzie istnieją, bo praca toczy się równolegle](../16-parallel-branches/README.pl.md).
