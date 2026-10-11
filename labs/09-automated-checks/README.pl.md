# Lab 09 — Maszyny mogą sprawdzać nudne rzeczy

## Sytuacja

Ostatnie code review poświęciło dziesięć minut na tabulatory i spacje,
nieużywany import oraz wybór rodzaju cudzysłowów. Żaden z tych tematów
nie dotyczył działania kodu. Zachowanie sprawdzają testy, choć Lab 08
pokazał, że obejmują tylko przewidziane przypadki. Spory o formatowanie
nadal zajmują czas, mimo że może je rozstrzygnąć narzędzie.

W tym labie trzy ścieżki używają różnych narzędzi. Python, Go i Java
inaczej wyznaczają granicę między formatowaniem a analizą statyczną.
Czytaj tylko sekcję swojej ścieżki.

## Cele nauki

Po tym labie potrafisz:

- Odróżnić zadania narzędzia formatującego, analizatora statycznego i
  testów.
- Dodać i skonfigurować narzędzie deweloperskie w manifeście projektu.
- Uruchomić narzędzie formatujące i analizator swojej ścieżki oraz
  odczytać ich wynik.

## Zanim zaczniesz

- Lab 08 ukończony w wybranej ścieżce. Błąd podatku jest naprawiony, a
  wszystkie testy przechodzą.

### Python

- Pracuj w `examples/restaurant-bill/python/`.

### Go

- Pracuj w `examples/restaurant-bill/go/`. `gofmt` i `go vet` są częścią
  toolchaina Go zainstalowanego w Labie 06, więc nie trzeba niczego
  dodawać.

### Java

- Pracuj w `examples/restaurant-bill/java/`. Ta ścieżka dodaje dwie
  wtyczki Gradle: narzędzie formatujące i analizator statyczny. Gradle
  pobierze je z Maven Central przy pierwszym uruchomieniu, podobnie jak
  JUnit 5 w Labie 06.

## Twoje zadanie

### Python

1. Dodaj `ruff` jako zależność deweloperską w `pyproject.toml` obok
   `pytest`, a następnie uruchom `uv sync`.
2. Dodaj sekcję `[tool.ruff]` do `pyproject.toml` z
   `target-version = "py313"` i `line-length = 100`.
3. Uruchom `uv run ruff format --check .`. Polecenie sprawdzi format
   plików bez ich zmieniania.
4. Uruchom `uv run ruff check .`. To polecenie szuka problemów w
   kodzie (nieużywanych importów, nieużywanych zmiennych i podobnych),
   co jest innym pytaniem niż formatowanie.
5. Tymczasowo dodaj nieużywany import (na przykład `import math`) na
   górę `billing/calculator.py`. Uruchom `uv run ruff check .` jeszcze
   raz i przeczytaj nazwę zgłoszonej reguły. Po sprawdzeniu komunikatu
   usuń import.
6. Napraw problemy znalezione w kodzie z Labów 06-08. Jeśli na przykład
   `ruff check .` zgłosi nieposortowany blok importów w teście,
   uporządkuj go.

### Go

1. Uruchom `gofmt -l .`. Polecenie *wyświetla* pliki, które nie są
   sformatowane zgodnie z `gofmt`, ale jego
   kod wyjścia to `0`, nawet gdy coś znajdzie, więc samo `gofmt -l .` w
   skrypcie przeszłoby po cichu niezależnie od wyniku. Naprawisz to w
   Labie 10. Na razie przyjmij, że każdy wyświetlony plik wymaga
   sformatowania.
2. Uruchom `go vet ./...`. Polecenie szuka błędów, których
   `gofmt` nie widzi (podejrzane wywołania `Printf`, nieosiągalny kod i
   podobne), co jest innym pytaniem niż formatowanie.
3. Tymczasowo zmień jeden ze specyfikatorów formatu `fmt.Printf` w
   `billing/cli.go`, aby nie pasował do typu argumentu. Możesz
   na przykład zmienić `"Total: $%.2f\n"` na `"Total: %d\n"`, gdzie
   argument wciąż jest `float64`. Uruchom `go vet ./...` jeszcze raz i
   przeczytaj, co raportuje. Zmień string formatu z powrotem, gdy już
   zobaczyłeś/aś komunikat.
4. Uruchom `go test ./...`, aby potwierdzić, że zestaw testów nadal
   przechodzi. `go vet` i `gofmt` nie uruchamiają testów.

### Java

1. Dodaj dwie wtyczki do bloku `plugins` w `build.gradle`:
   ```gradle
   id 'pmd'
   id 'com.diffplug.spotless' version '8.10.3'
   ```
2. Dodaj blok `spotless` konfigurujący Google Java Format:
   ```gradle
   spotless {
       java {
           googleJavaFormat()
       }
   }
   ```
3. Uruchom `./gradlew spotlessCheck`. Google Java Format używa wcięć
   dwuspacjowych, prawdopodobnie innych niż w Labach 06-08. Pierwsze
   uruchomienie może zgłosić naruszenia w Twoich plikach. Nie oznacza to
   nieudanego testu.
4. Uruchom `./gradlew spotlessApply`, aby przeformatować pliki, a potem
   ponownie `./gradlew spotlessCheck`.
5. Utwórz `config/pmd/ruleset.xml`:
   ```xml
   <?xml version="1.0"?>
   <ruleset name="restaurant-bill"
       xmlns="http://pmd.sourceforge.net/ruleset/2.0.0"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://pmd.sourceforge.net/ruleset/2.0.0 https://pmd.sourceforge.io/ruleset_2_0_0.xsd">
       <description>Minimal ruleset for the restaurant-bill Java track: a couple of
       real-mistake checks, not a full default category (which flags
       System.out.println, the whole point of this CLI).</description>

       <rule ref="category/java/bestpractices.xml/UnusedLocalVariable"/>
       <rule ref="category/java/bestpractices.xml/UnusedPrivateField"/>
       <rule ref="category/java/errorprone.xml/EmptyCatchBlock"/>
   </ruleset>
   ```
   Ten projekt celowo **nie** używa pełnych domyślnych kategorii reguł
   PMD. Jedna z nich (`SystemPrintln`) zgłosiłaby każde wywołanie
   `System.out.printf` w `Cli.java`, a wyświetlanie wyniku jest zadaniem
   tego programu.
   Mały, ręcznie dobrany zestaw reguł jest tutaj właściwym wyborem.
6. Dodaj blok `pmd` do `build.gradle`:
   ```gradle
   pmd {
       toolVersion = '7.28.0'
       ruleSetFiles = files('config/pmd/ruleset.xml')
       ruleSets = []
   }
   ```
7. Uruchom `./gradlew pmdMain`. Polecenie powinno przejść, ponieważ kod
   z Labów 06-08 nie ma nieużywanych zmiennych lokalnych ani pól oraz
   nie zawiera pustych bloków `catch`.
8. Tymczasowo dodaj naprawdę nieużywaną lokalną zmienną do `Cli.java`
   (nie nazywaj jej `unused...` ani `ignored...`; PMD celowo ignoruje
   takie nazwy).
   Uruchom `./gradlew pmdMain` jeszcze raz i przeczytaj, co zgłasza.
   Usuń zmienną, gdy już zobaczyłeś/aś komunikat.
9. Uruchom `./gradlew test`, aby potwierdzić, że zestaw nadal przechodzi.
   Spotless i PMD nie uruchamiają testów.

## Kryteria akceptacji

### Python

- `pyproject.toml` zawiera `ruff` jako zależność deweloperską i ma sekcję
  `[tool.ruff]`.
- `uv run ruff format --check .` nie raportuje plików do zmiany.
- `uv run ruff check .` nie raportuje problemów.

### Go

- `gofmt -l .` niczego nie wyświetla.
- `go vet ./...` niczego nie raportuje.

### Java

- `build.gradle` konfiguruje oba bloki `spotless` i `pmd` pokazane
  powyżej, a `config/pmd/ruleset.xml` istnieje.
- `./gradlew spotlessCheck` i `./gradlew pmdMain` kończą się sukcesem.

We wszystkich ścieżkach zestaw testów z Labów 07-08 nadal przechodzi.
Żadne z tych narzędzi nie zmieniło działania programu.

## Weryfikacja

### Python

```bash
cd examples/restaurant-bill/python
uv run ruff format --check .
uv run ruff check .
uv run pytest
cd -
```

Oczekiwany wynik: oba polecenia Ruff nie zgłaszają niczego do
naprawienia, a `pytest` nadal przechodzi.

### Go

```bash
cd examples/restaurant-bill/go
gofmt -l .
go vet ./...
go test ./...
cd -
```

Oczekiwany wynik: `gofmt -l .` i `go vet` niczego nie wyświetlają, a
`go test` przechodzi.

### Java

```bash
cd examples/restaurant-bill/java
./gradlew spotlessCheck
./gradlew pmdMain
./gradlew test
cd -
```

Oczekiwany wynik: wszystkie trzy polecenia kończą się sukcesem.

## Zastanów się

- Które z użytych narzędzi może stwierdzić, że kod jest „poprawny”?
  Które potrafią tylko ocenić jego spójność lub wykryć oczywiste błędy?
- Dlaczego narzędzie formatujące i analizator statyczny działają jako
  dwa osobne kroki, a nie jeden?

## Jeśli utkniesz

### Python

- **Podpowiedź 1:** `uv add --dev ruff` dodaje zależność za Ciebie
  zamiast ręcznego edytowania `pyproject.toml`, jeśli nie chcesz
  edytować TOML ręcznie.
- **Podpowiedź 2:** `ruff format` przepisuje pliki, żeby odpowiadały
  jego stylowi; `ruff format --check` tylko raportuje, co *by* się
  zmieniło, bez modyfikowania plików. Najpierw użyj `--check`.
- **Podpowiedź 3:** Jeśli `ruff check .` nic nie raportuje na Twoim
  kodzie, wszystko jest w porządku. Kod z Labów 06-08 nie zawierał
  problemów wykrywanych przez Ruffa.

### Go

- **Podpowiedź 1:** `gofmt -l .` tylko *wyświetla* nazwy plików i zawsze
  kończy się kodem `0`. Skrypt musi sam sprawdzić, czy wynik jest pusty.
  Taki mechanizm zbudujesz w Labie 10.
- **Podpowiedź 2:** `gofmt -w .` (zamiast `-l`) przepisuje
  pliki, naprawiając formatowanie, tak samo jak `ruff format` (bez
  `--check`) robi dla Pythona.
- **Podpowiedź 3:** Jeśli `go vet ./...` nic nie raportuje na Twoim
  kodzie, wszystko jest w porządku. Kod z Labów 06-08 nie zawierał
  problemów wykrywanych przez `go vet`.

### Java

- **Podpowiedź 1:** Jeśli `spotlessApply` wydaje się nic nie robić,
  sprawdź, czy pliki zostały zapisane. Niektóre edytory przechowują
  starą zawartość do ponownego otwarcia pliku.
- **Podpowiedź 2:** Ostrzeżenie PMD o "wrong java version" dla
  `auxClasspath` jest niegroźne dla tego projektu. Dotyczy sposobu
  wykrywania classpath, a nie problemu z kodem.
- **Podpowiedź 3:** Jeśli `pmdMain` niczego nie zgłasza w Twoim
  kodzie, wszystko jest w porządku. Kod z Labów 06-08 nie zawierał
  żadnego z trzech problemów sprawdzanych przez ten zestaw reguł.

Przed przejściem dalej zacommituj i wypchnij wszystkie zmiany poleceniem
`git add -A && git commit -m "..."; git push`. Kolejne laby nie wymagają
jeszcze czystego drzewa, ale Akt IV, zaczynający się od Labu 16, już tak.

## Co dalej

Masz trzy rodzaje automatycznej informacji zwrotnej: testy, formatowanie
i analizę statyczną. Na razie trzeba pamiętać kilka poleceń oraz ich
kolejność. W następnym labie połączysz je w jedno polecenie.

Przejdź do [Lab 10 — Jeden oczywisty sposób sprawdzania projektu](../10-one-way-to-check/README.pl.md).
