# Lab 14 — Jeden kontrakt, trzy języki

## Sytuacja

Kuchnia chce otrzymywać powiadomienie, gdy zamówienie jest gotowe. Na
razie wystarczy komunikat w konsoli, ale później może pojawić się e-mail
albo SMS. Trzy zespoły przygotowały ten sam niewielki kontrakt w
Pythonie, Go i Javie. Idea jest wspólna, choć każdy język zapisuje ją
inaczej. Zmieniasz tylko implementację w wybranym języku.

## Cele nauki

Po tym labie potrafisz:

- Wyjaśnić, co sprawia, że coś jest „kontraktem”, niezależnie od
  składni użytej do jego zapisania.
- Wyjaśnić, jak wybrany język rozpoznaje spełnienie kontraktu, i opisać
  różnice względem pozostałych dwóch języków na podstawie ich kodu.
- Dodać w wybranym języku nową implementację istniejącego kontraktu i
  potwierdzić jej działanie testem.

## Zanim zaczniesz

- Najpierw ukończ laby 01-05. Laby 06-13 nie są potrzebne do wykonania
  tego konkretnego ćwiczenia.
- Potrzebujesz tylko narzędzi dla **wybranego języka**.

### Python

- Zainstaluj `uv` i pracuj w `examples/notifier/python/`.

### Go

- Zainstaluj Go 1.27.x i pracuj w `examples/notifier/go/`.

### Java

- Zainstaluj JDK 21 i pracuj w `examples/notifier/java/`.

## Twoje zadanie

### Krok 1. Przeczytaj wszystkie trzy kontrakty

Przeczytaj poniższe pliki, nawet jeśli masz zainstalowane narzędzia
tylko dla jednego języka. Do zapoznania się z kodem źródłowym nie
trzeba go uruchamiać.

- Python: `examples/notifier/python/notifier/notifier.py`
- Go: `examples/notifier/go/notifier.go`
- Java: `examples/notifier/java/Notifier.java`,
  `examples/notifier/java/ConsoleNotifier.java`,
  `examples/notifier/java/InMemoryNotifier.java`,
  `examples/notifier/java/ReceiptService.java`

Znajdź strukturę, która powtarza się we wszystkich trzech językach.
Typ `Notifier` opisuje jedną metodę, a co najmniej dwie implementacje
są przekazywane do funkcji. Funkcja wywołuje tę metodę, nie wiedząc,
którą implementację otrzymała. Python korzysta z `Protocol`, a Go i
Java z interfejsów.

### Krok 2. Uruchom test dla swojej ścieżki

### Python

```bash
cd examples/notifier/python && uv run pytest -v && cd - > /dev/null
```

### Go

```bash
cd examples/notifier/go && go test ./... && cd - > /dev/null
```

### Java

```bash
cd examples/notifier/java && javac *.java -d out && java -cp out NotifierCheck && cd - > /dev/null
```

### Krok 3. Dodaj `SilentNotifier` w swojej ścieżce

### Python

W `notifier/notifier.py` dodaj klasę `SilentNotifier` z metodą `send`,
która nic nie robi. Nie musisz używać dziedziczenia `class
SilentNotifier(Notifier)`, ponieważ działa tu duck typing. Dodaj test w
`tests/test_notifier.py`, który potwierdza, że
`send_receipt_ready(SilentNotifier(), "A123")` nie zgłasza wyjątku.

### Go

W `notifier.go` dodaj `type SilentNotifier struct{}` z metodą
`Send(message string)` z pustym ciałem. Dodaj test w
`notifier_test.go`, który potwierdza, że `SendReceiptReady(SilentNotifier{},
"A123")` nie kończy się paniką.

### Java

Dodaj klasę `SilentNotifier implements Notifier` w nowym pliku
`SilentNotifier.java`, z pustym ciałem metody `send`. W
`NotifierCheck.java` dodaj drugie sprawdzenie, które potwierdza, że
`ReceiptService.sendReceiptReady(new SilentNotifier(), "A123")` nie
zgłasza wyjątku.

### Krok 4. Porównaj, jak każdy język rozpoznaje kontrakt

Znasz już wszystkie trzy implementacje i masz za sobą pracę z jedną z
nich. Poniższe przykłady pokazują, co się dzieje, gdy typ nie spełnia
kontraktu. Wyniki zostały sprawdzone na potrzeby kursu, więc możesz
porównać języki bez instalowania narzędzi dla pozostałych dwóch.

**Java.** Klasa z metodą `send(String message)`, ale bez deklaracji
`implements Notifier`, nie skompiluje się, gdy spróbujesz przekazać ją
w miejscu oczekującym `Notifier`. Sama obecność metody nie wystarczy.

```text
TryBad.java:4: error: incompatible types: BadNotifier cannot be converted to Notifier
        ReceiptService.sendReceiptReady(n, "A123");
                                        ^
```

**Go.** Struktura z *błędnie zapisaną* nazwą metody (`Sand` zamiast
`Send`) nie skompiluje się, gdy spróbujesz przekazać ją w miejscu
oczekującym interfejsu `Notifier`. Go sprawdza dopasowanie strukturalne
i nie wymaga słowa kluczowego `implements`.

```text
./notifier.go:20:19: cannot use BadNotifier{} (value of struct type BadNotifier) as Notifier value in argument to SendReceiptReady: BadNotifier does not implement Notifier (missing method Send)
```

**Python.** Obiekt z *błędnie zapisaną* nazwą metody (`sand` zamiast
`send`) zostanie poprawnie sparsowany i nie spowoduje błędu aż do
wykonania wiersza wywołującego `.send(...)`.

```text
Traceback (most recent call last):
  File "notifier_check.py", line 18, in <module>
    send_receipt_ready(BadNotifier(), "A123")
  File "notifier_check.py", line 9, in send_receipt_ready
    notifier.send(f"Order {order_id} is ready.")
AttributeError: 'BadNotifier' object has no attribute 'send'. Did you mean: 'sand'?
```

Jeśli ta ścieżka kodu nigdy się nie wykona, na przykład dlatego, że nic
nie używa implementacji z literówką, Python nie zgłosi wyjątku. Metoda
nie została sprawdzona, więc błąd pozostał niewykryty.

### Krok 5. Odpowiedz własnymi słowami

W pliku notatek `labs/14-one-contract-three-languages/my-notes.md`
odpowiedz:

1. Jak wybrany język rozpoznaje, że
   `SilentNotifier` spełnia `Notifier`? Wskaż dokładny mechanizm
   (klauzula `implements` sprawdzana przez kompilator, strukturalne
   dopasowanie sprawdzane przez kompilator albo brak sprawdzania).
2. Korzystając ze zweryfikowanych wyników z kroku 4, wskaż, który język
   wykryje literówkę w nazwie metody *najszybciej*. Czy stanie się to
   przed uruchomieniem programu, czy dopiero po wykonaniu błędnej
   ścieżki kodu?
3. Jeśli osoba z zespołu przekaże Ci klasę z właściwą metodą, która nie
   spełnia kontraktu (brak `implements` w Javie, błędnie zapisana metoda
   w Go albo Pythonie), jaka jest konkretna,
   obserwowalna różnica w sposobie zgłoszenia problemu przez narzędzia
   każdego języka?

## Opcjonalne ćwiczenie

Jeśli masz narzędzia dla pozostałych dwóch języków, możesz uruchomić
wszystkie trzy polecenia z kroku 2 i zbudować `SilentNotifier` w każdym
języku. Nie jest to wymagane; kryteria akceptacji dotyczą tylko
wybranej ścieżki.

## Kryteria akceptacji

- W wybranym języku działa `SilentNotifier`, a nowe sprawdzenie
  przechodzi razem z istniejącymi sprawdzeniami `ConsoleNotifier` i
  `InMemoryNotifier`.
- `my-notes.md` odpowiada własnymi słowami na wszystkie trzy pytania z
  kroku 5 i opiera się na zweryfikowanych wynikach z kroku 4.

## Weryfikacja

### Python

```bash
cd examples/notifier/python && uv run pytest -v && cd - > /dev/null
test -f labs/14-one-contract-three-languages/my-notes.md && echo "notes exist"
```

### Go

```bash
cd examples/notifier/go && go test ./... -v && cd - > /dev/null
test -f labs/14-one-contract-three-languages/my-notes.md && echo "notes exist"
```

### Java

```bash
cd examples/notifier/java && javac *.java -d out && java -cp out NotifierCheck && cd - > /dev/null
test -f labs/14-one-contract-three-languages/my-notes.md && echo "notes exist"
```

Testy wybranej ścieżki, łącznie z nowym sprawdzeniem `SilentNotifier`,
powinny przejść, a plik z notatkami powinien istnieć.

## Zastanów się

- Interfejs Go i `Protocol` Pythona pozwalają spełnić kontrakt samą
  obecnością właściwej metody, bez jawnej deklaracji. Jednak
  kompilator Go sprawdza to dopasowanie, gdy przekazujesz wartość jako
  interfejs. W tym labie Python nie przeprowadza takiej kontroli. Co
  trzeba zrobić, aby Python również przeprowadzał taką kontrolę?
  Istnieje do tego narzędzie; jakiego polecenia brakuje w tym labie?
- `Protocol` Pythona sam w sobie daje Ci *dokumentację* kontraktu, nie
  *wymuszenie*. Czy to wada Pythona, czy kompromis? Co, jak sobie
  wyobrażasz, programiści Pythona zyskują w zamian za brak tej
  kontroli?

## Jeśli utkniesz

### Python

- **Podpowiedź 1.** Ciało metody `send` w `SilentNotifier` to po
  prostu `pass`.
- **Podpowiedź 2.** Nie potrzebujesz `class SilentNotifier(Notifier)`.
  Nic w tym labie nie wymusza `Protocol`, więc zwykłe `class
  SilentNotifier:` z odpowiadającą metodą `send` wystarczy.
- **Podpowiedź 3.** Wystarczy kilka linii kodu i jedno polecenie:
  `uv run pytest -v`.

### Go

- **Podpowiedź 1.** Ciało metody `Send` w `SilentNotifier` to po
  prostu puste `{}`.
- **Podpowiedź 2.** Nie musisz deklarować, że `SilentNotifier`
  implementuje `Notifier`. Go sprawdza to, gdy przekazujesz
  `SilentNotifier{}` jako `Notifier`.
- **Podpowiedź 3.** Wystarczy kilka linii kodu i jedno polecenie:
  `go test ./...`.

### Java

- **Podpowiedź 1.** Ciało metody `send` w `SilentNotifier` to po
  prostu puste `{}`.
- **Podpowiedź 2.** Brak `implements Notifier` nie zatrzyma
  samodzielnej kompilacji `SilentNotifier`, ale uniemożliwi przekazanie
  takiego obiektu do `sendReceiptReady`, który oczekuje `Notifier`.
  Możesz odtworzyć przykład z kroku 4 we własnej klasie, usuwając tę
  deklarację.
- **Podpowiedź 3.** Nie potrzebujesz narzędzia do budowania projektu.
  `javac *.java -d out && java -cp out NotifierCheck` to jedyna
  komenda, której potrzebujesz.

Przed przejściem dalej utwórz commit ze wszystkim z tego laba i
wypchnij go do repozytorium
(`git add -A && git commit -m "..."; git push`). Nic później jeszcze
nie wymaga czystego drzewa roboczego, ale Akt IV (od labu 16) już tak.
Warto od razu wyrobić sobie ten nawyk.

## Co dalej

Kody rabatowe z labów 12-13 i powiadomienia z tego labu mają wspólną
strukturę. Kod wywołujący wybiera jedno z kilku wymiennych zachowań,
zamiast polegać na łańcuchu warunków ukrytym w logice biznesowej. W
następnym labie nazwiesz tę strukturę.

Przejdź do [Lab 15 — Wzorce bez bałwochwalstwa](../15-patterns-without-worship/README.pl.md).
