# Lab 19 — Repozytorium powinno sprawdzać samo siebie

## Sytuacja

Zmiana scalona tydzień temu zepsuła testy na `main`. Autor zapomniał je
uruchomić, a osoba przeglądająca zaufała opisowi pull requesta. Problem
wyszedł na jaw dopiero wtedy, gdy ktoś ręcznie uruchomił program, a ten
zakończył się błędem.

## Cele nauki

Po tym labie potrafisz:

- Napisać prosty przepływ pracy GitHub Actions uruchamiany przy każdym
  pushu i pull requeście.
- Wyjaśnić działanie każdego kroku CI, zamiast traktować YAML jak magię.
- Traktować wynik kontroli CI jako dowód, zamiast ufać samemu opisowi.

## Zanim zaczniesz

- Najpierw ukończ lab 18. `main` powinien zawierać funkcję reorder-report
  scaloną przez pull request w wybranej ścieżce.
- Pracuj w katalogu głównym repozytorium. Plik przepływu pracy znajduje się
  poza `examples/team-inventory/`, w `.github/workflows/`.
- Jeśli Twoje repozytorium jest forkiem, GitHub domyślnie wyłącza na
  nim przepływy GitHub Actions. Otwórz zakładkę **Actions** swojego forka i
  kliknij **"I understand my workflows, go ahead and enable them"**,
  zanim przepływ z tego laba będzie mógł się uruchomić.
- Główne repozytorium kursu ma własny przepływ utrzymaniowy,
  `.github/workflows/course-health.yml`, który sprawdza cały kurs. Jest
  celowo ograniczony do głównego repozytorium. W jego definicji zobaczysz
  warunek sprawdzający nazwę repozytorium. Na Twoim forku GitHub oznaczy
  to zadanie jako **skipped** (pominięte), a nie jako zakończone błędem
  lub brakujące. Przepływ tworzony w tym labie działa na Twoim forku i
  sprawdza tylko `examples/team-inventory/<twój-język>`.

## Twoje zadanie

1. Utwórz gałąź `feature/ci-pipeline` z `main`.
2. Utwórz `.github/workflows/team-inventory-ci.yml` (utwórz
   `.github/workflows/`, jeśli nie istnieje). Skopiuj **kompletny**
   przepływ swojej ścieżki do tego jednego pliku, dokładnie w pokazanej
   postaci. Nie dodawaj wcześniej żadnej innej zawartości. Każdy przykład
   jest całym plikiem i zawiera klucz `on:` tylko raz. Wszystkie trzy
   ścieżki korzystają z tego samego wyzwalacza `on:
   [push, pull_request]`, ale to nie jest osobny fragment do dodania
   nad przykładem wybranej ścieżki.

### Python

```yaml
name: team-inventory CI

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
        working-directory: examples/team-inventory/python
        run: uv sync --locked

      - name: Run tests
        working-directory: examples/team-inventory/python
        run: uv run pytest
```

Jeśli `uv.lock` nie zgadza się z `pyproject.toml`, polecenie
`uv sync --locked` przerywa zadanie zamiast po cichu aktualizować plik.
Właśnie takie rozbieżności ma wykrywać CI. Wersje `3.13` i `0.11.21`
są zgodne z wymaganiami kursu. Sprawdź tabelę narzędzi w głównym
[`README.pl.md`](../../README.pl.md) lub plik `.python-version` w katalogu
głównym repozytorium, a nie konfigurację wybranego edytora.

### Go

```yaml
name: team-inventory CI

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
        working-directory: examples/team-inventory/go
        run: go test ./...
```

Wersja `1.27` jest zgodna z wymaganiami kursu. Zobacz tabelę narzędzi
w głównym [`README.pl.md`](../../README.pl.md).

### Java

```yaml
name: team-inventory CI

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
        working-directory: examples/team-inventory/java
        run: ./gradlew test
```

`gradle/actions/setup-gradle@v6` sprawdza sumę kontrolną plików Gradle
Wrappera przed uruchomieniem `./gradlew` i przechowuje pobrane pliki
Gradle w pamięci podręcznej. **Nie** instaluje Gradle'a globalnie.
Polecenie `./gradlew test` nadal korzysta z wrappera zapisanego w
repozytorium, tak samo jak na Twoim komputerze.

## Wszystkie ścieżki

3. Utwórz commit, wypchnij gałąź i otwórz pull request, tak jak w labie
   18. Zacznij od interfejsu WWW i jeszcze raz sprawdź repozytorium oraz
   gałąź docelową.
4. Otwórz zakładkę **Checks** pull requesta i obserwuj działanie
   przepływu. Sprawdź, czy kontrola zakończyła się powodzeniem.
5. Celowo spraw, aby jeden test lokalnie nie przechodził, na przykład
   zmieniając asercję na fałszywą. Utwórz commit i wypchnij go. Obserwuj,
   jak kontrola w pull requeście zmienia kolor na **czerwony**. Następnie
   wycofaj tę zmianę, wypchnij kolejny commit i poczekaj na zielony wynik.
6. Scal pull request, gdy wszystkie kontrole są zielone.

## Kryteria akceptacji

- Plik `.github/workflows/team-inventory-ci.yml` istnieje, sprawdza
  `examples/team-inventory/<twój-język>` i uruchamia się po wypchnięciu
  zmian oraz dla pull requestów.
- Na prawdziwym pull requeście udało Ci się zaobserwować zarówno czerwony
  wynik dla testu, który nie przechodził, jak i zielony po jego naprawie.
- Po scaleniu zmian kontrole na `main` są zielone.

## Weryfikacja

Żadne lokalne polecenie nie zastąpi obserwowania działania kontroli na
GitHubie. Ta obserwacja *jest* sensem tego laba. Lokalnie możesz jedynie
odtworzyć polecenia wykonywane przez przepływ:

### Python

```bash
cd examples/team-inventory/python
uv sync --locked
uv run pytest
cd -
```

### Go

```bash
cd examples/team-inventory/go
go test ./...
cd -
```

### Java

```bash
cd examples/team-inventory/java
./gradlew test
cd -
```

Jeśli testy przechodzą lokalnie, a plik YAML uruchamia te same polecenia
w tym samym katalogu, wynik kontroli pull requesta powinien być taki sam.

## Zastanów się

- W labie 18 osoba przeglądająca mogła pominąć uruchomienie testów i
  zaufać opisowi pull requesta. Co się zmieniło po dodaniu przepływu?
  Kto lub co odpowiada teraz za wykrycie nieprzetestowanej zmiany?
- Przepływ wykonuje te same polecenia, które od kilku labów uruchamiasz
  ręcznie. Co daje ich automatyzacja, skoro same polecenia się nie
  zmieniły?

## Jeśli utkniesz

### Python

- **Podpowiedź 1.** Minimalny przepływ potrzebuje `on:`, sekcji `jobs:`
  z co najmniej jednym zadaniem oraz listy `steps:`. Powinna ona zawierać
  pobranie repozytorium, konfigurację Pythona, instalację `uv` oraz
  polecenia `uv sync --locked` i `uv run pytest`. Powyższa wersja została
  sprawdzona na potrzeby tego kursu.
- **Podpowiedź 2.** Ustaw `working-directory:
  examples/team-inventory/python` dla kroków z poleceniami
  `uv sync --locked` i `uv run pytest`. Domyślnym katalogiem roboczym jest
  katalog główny repozytorium.
- **Podpowiedź 3.** Jeśli `uv sync --locked` kończy się błędem w CI, ale
  `uv sync` działa lokalnie, plik `uv.lock` jest nieaktualny. Uruchom
  lokalnie `uv lock`, zapisz zaktualizowany plik w commicie i ponownie
  wypchnij zmiany.

### Go

- **Podpowiedź 1.** Minimalny przepływ potrzebuje `on:`, sekcji `jobs:`
  z co najmniej jednym zadaniem oraz listy `steps:`. Powinna ona zawierać
  pobranie repozytorium, konfigurację Go i `go test ./...`. Osobny krok
  instalowania lub synchronizowania zależności nie jest potrzebny.
  System modułów Go zrobi to podczas `go test`.
- **Podpowiedź 2.** Ustaw `working-directory: examples/team-inventory/go`
  dla kroku testowego. Domyślnym katalogiem roboczym jest katalog główny
  repozytorium.
- **Podpowiedź 3.** Jeśli przepływ nie może znaleźć pakietu, sprawdź, czy
  plik `go.mod` jest zapisany w commicie. Plik nieśledzony przez Git działa
  na Twoim komputerze, ale nie jest dostępny dla GitHub Actions.

### Java

- **Podpowiedź 1.** Minimalny przepływ potrzebuje `on:`, sekcji `jobs:`
  z co najmniej jednym zadaniem oraz listy `steps:`. Powinna ona zawierać
  pobranie repozytorium, konfigurację JDK, `setup-gradle` i
  `./gradlew test`.
- **Podpowiedź 2.** Ustaw `working-directory:
  examples/team-inventory/java` dla kroku testowego. Domyślnym katalogiem
  roboczym jest katalog główny repozytorium.
- **Podpowiedź 3.** Jeśli pojawi się błąd `gradlew: Permission denied`,
  plik `gradlew` stracił uprawnienie do wykonywania. Uruchom
  `chmod +x examples/team-inventory/java/gradlew`, zapisz zmianę trybu
  pliku w commicie i ponownie ją wypchnij.

Przed przejściem dalej utwórz commit ze wszystkimi zmianami z tego laba
i wypchnij go (`git add -A && git commit -m "..."; git push`). W kolejnych
labach przyda się czyste drzewo robocze, więc warto już teraz wyrobić sobie
ten nawyk.

## Co dalej

Masz testy, przegląd kodu i CI. Kiedy przy takim zestawie zabezpieczeń
można uznać zmianę za ukończoną?

Przejdź do [Lab 20 — Co znaczy "zrobione"?](../20-definition-of-done/README.pl.md).
