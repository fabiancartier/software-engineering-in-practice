# Lab 10 — Jeden oczywisty sposób sprawdzania projektu

## Sytuacja

Nowa osoba w projekcie pyta: „Jak uruchomić testy? Najpierw było
formatowanie czy analiza?”. Wykonujesz te polecenia tak często, że nie
zastanawiasz się już nad kolejnością. Nowa osoba nie powinna jej
zapamiętywać.

## Cele nauki

Po tym labie potrafisz:

- Opakować sekwencję poleceń w mały, czytelny skrypt shellowy.
- Wyjaśnić, dlaczego skrypt w projekcie jest lepszy niż
  instrukcja w README, którą czytelnik musi skopiować ręcznie.
- Wyjaśnić, co oznacza brak ukrytych mechanizmów we własnej
  automatyzacji.

## Zanim zaczniesz

- Lab 09 ukończony w wybranej ścieżce. Formatowanie, analiza i testy
  uruchomione osobno kończą się sukcesem.
- **Uwaga dla Windows:** ten kurs działa na Git Bash na Windowsie, nie
  WSL i nie Docker. Git nie przechowuje bitu wykonywalności Unix tak
  samo jak Linux i macOS, więc skrypt, który działa, gdy wpisujesz
  `./scripts/check.sh` na swojej maszynie, może nie być bezpośrednio
  wykonywalny na maszynie kolegi z zespołu. Przenośny sposób
  wywoływania każdego z tych skryptów, na każdym systemie, który ten
  kurs wspiera, to:
  ```bash
  bash scripts/check.sh
  ```
  To zawsze działa, bo nigdy nie zależy od własnego bitu wykonywalności
  pliku. Polecenie jawnie wskazuje interpreter `bash`. Mimo to oznacz
  skrypty jako wykonywalne poleceniem `chmod +x scripts/*.sh`.
  Bezpośrednie uruchomienie jest wygodnym skrótem na systemach, które
  je obsługują.

### Python

- Pracuj w `examples/restaurant-bill/python/`.

### Go

- Pracuj w `examples/restaurant-bill/go/`.

### Java

- Pracuj w `examples/restaurant-bill/java/`. Skrypty korzystają z
  dołączonego `./gradlew`, a nie z globalnego `gradle`. Projekt nie
  zakłada, że Gradle jest zainstalowany globalnie.

## Twoje zadanie

Utwórz w katalogu `scripts/` cztery skrypty. Każdy z nich ma działać z
dowolnego katalogu, ponieważ sam przechodzi do katalogu głównego
projektu.

1. `scripts/test.sh` uruchamia zestaw testów.
2. `scripts/check.sh` uruchamia kontrolę formatowania i analizator
   statyczny (w tej kolejności), potem zestaw testów.
3. `scripts/format.sh` przeformatowuje kod (nie tylko
   kontrola).
4. `scripts/run.sh` uruchamia aplikację.

Zrób wszystkie cztery wykonywalne (`chmod +x scripts/*.sh`). Każdy
skrypt powinien być wystarczająco krótki, żeby czytanie go od góry do
dołu wyjaśniało jego działanie bez dodatkowej dokumentacji.

### Python

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(dirname "$0")/.."

uv run pytest
```

To jest `scripts/test.sh` w całości. `scripts/check.sh` uruchamia
`uv run ruff format --check .`, potem `uv run ruff check .`, potem
`uv run pytest`. `scripts/format.sh` uruchamia `uv run ruff format .`.
`scripts/run.sh` uruchamia `uv run python main.py`.

### Go

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(dirname "$0")/.."

go test ./...
```

To jest `scripts/test.sh` w całości. `scripts/format.sh` uruchamia
`gofmt -w .`. `scripts/run.sh` uruchamia `go run main.go`.
`scripts/check.sh` jest jedynym skryptem, który potrzebuje więcej niż
jednego polecenia ze względu na zachowanie `gofmt -l` z Labu 09.
Narzędzie wyświetla problemy, ale nadal kończy się kodem `0`:

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(dirname "$0")/.."

echo "== gofmt =="
unformatted="$(gofmt -l .)"
if [ -n "$unformatted" ]; then
  echo "Not formatted:"
  echo "$unformatted"
  exit 1
fi

echo "== go vet =="
go vet ./...

echo "== go test =="
go test ./...
```

### Java

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(dirname "$0")/.."

./gradlew test --console=plain
```

To jest `scripts/test.sh` w całości. `scripts/format.sh` uruchamia
`./gradlew spotlessApply --console=plain`. `scripts/run.sh` uruchamia
`./gradlew run --console=plain`. `scripts/check.sh` uruchamia wszystkie
trzy kontrole Gradle po kolei:

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(dirname "$0")/.."
export PMD_JAVA_DISABLE_AUX_CLASSPATH_WARNINGS=true

echo "== spotless (formatter) =="
./gradlew spotlessCheck --console=plain

echo "== pmd (static analysis) =="
./gradlew pmdMain --console=plain

echo "== tests =="
./gradlew test --console=plain
```

(Eksportowana zmienna wycisza niegroźne ostrzeżenie PMD z Labu 09. Nie
zmienia zakresu kontroli.)

## Kryteria akceptacji

- Wszystkie cztery skrypty istnieją, są wykonywalne i działają, gdy
  wywołane przez `bash scripts/<name>.sh` z innego katalogu startowego
  (np. Twojego katalogu domowego).
- `scripts/check.sh` kończy się statusem niezerowym, jeśli
  formatowanie, analiza statyczna albo testy nie przechodzą. Skrypt ma
  wyraźnie zgłosić błąd i przerwać działanie.
- Przeczytanie jednego skryptu zajmuje mniej niż trzydzieści sekund.

## Weryfikacja

### Python

```bash
cd ~
bash /path/to/examples/restaurant-bill/python/scripts/test.sh
bash /path/to/examples/restaurant-bill/python/scripts/check.sh
bash /path/to/examples/restaurant-bill/python/scripts/run.sh
cd -
```

### Go

```bash
cd ~
bash /path/to/examples/restaurant-bill/go/scripts/test.sh
bash /path/to/examples/restaurant-bill/go/scripts/check.sh
bash /path/to/examples/restaurant-bill/go/scripts/run.sh
cd -
```

### Java

```bash
cd ~
bash /path/to/examples/restaurant-bill/java/scripts/test.sh
bash /path/to/examples/restaurant-bill/java/scripts/check.sh
bash /path/to/examples/restaurant-bill/java/scripts/run.sh
cd -
```

Zastąp `/path/to/` rzeczywistą ścieżką repozytorium. Wszystkie trzy
polecenia powinny zakończyć się sukcesem bez ręcznej zmiany katalogu.

## Zastanów się

- Co stałoby się ze `scripts/check.sh`, gdyby jedno z jego poleceń
  zakończyło się błędem, a skrypt działał dalej? Która
  linia w Twoim skrypcie temu zapobiega?
- Czy jest coś w tym, co te skrypty robią, co nie jest widoczne po
  samym ich przeczytaniu? Jeśli ktoś z zespołu zapyta „Co robi
  `check.sh`?”, czy wystarczy pokazać
  mu plik?

## Jeśli utkniesz

### Python

- **Podpowiedź 1:** Zacznij każdy skrypt od `#!/usr/bin/env bash` i
  `set -euo pipefail`. Druga linia zatrzymuje skrypt przy pierwszym
  poleceniu zakończonym błędem.
- **Podpowiedź 2:** Aby skrypt działał niezależnie od bieżącego
  katalogu, umieść `cd "$(dirname "$0")/.."` blisko góry,
  zaraz po `set -euo pipefail`.
- **Podpowiedź 3:** `chmod +x scripts/*.sh` nadaje uprawnienie do
  uruchamiania wszystkim czterem skryptom.

### Go

- **Podpowiedź 1:** Tutaj również użyj `set -euo pipefail`. Bez niego
  `scripts/check.sh` uruchomiłby `go vet` nawet wtedy, gdy
  `gofmt` znalazł problem, co podważa sens kontroli.
- **Podpowiedź 2:** `unformatted="$(gofmt -l .)"` przechwytuje wynik
  polecenia do zmiennej zamiast od razu go wyświetlać. `[ -n
  "$unformatted" ]` sprawdza następnie, czy zmienna jest niepusta.
- **Podpowiedź 3:** Jeśli `scripts/run.sh` nie może znaleźć `main.go`,
  gdy jest wywołany z innego katalogu, sprawdź, czy linia
  `cd "$(dirname "$0")/.."` znajduje się bezpośrednio po
  `set -euo pipefail`.

### Java

- **Podpowiedź 1:** `--console=plain` utrzymuje wynik Gradle prosty i
  czytelny dla skryptów. Bez tej opcji formatowanie terminalowe Gradle
  może wyglądać nieczytelnie po przekierowaniu do pliku lub logu.
- **Podpowiedź 2:** Jeśli `scripts/run.sh` wydaje się wisieć pierwszy
  raz po sklonowaniu repozytorium, Gradle prawdopodobnie pobiera swoją
  dystrybucję, podobnie jak przy pierwszym uruchomieniu w Labie 06.
- **Podpowiedź 3:** Wszystkie cztery skrypty wywołują `./gradlew`,
  a nie samo `gradle`. Jeśli jeden z nich używa `gradle`, to
  prawdopodobnie jest przyczyną problemu.

Przed przejściem dalej zacommituj i wypchnij wszystkie zmiany poleceniem
`git add -A && git commit -m "..."; git push`. Akt IV, zaczynający się
od Labu 16, zakłada czyste drzewo robocze.

## Co dalej

Jednoplikowy monolit stał się małym, dobrze przetestowanym projektem ze
spójnym zestawem kontroli. Akt II jest kompletny we wszystkich trzech
ścieżkach. Pozostała część kursu aż do Aktu VI również je obsługuje:
Go i Java prowadzą teraz aż do projektu końcowego obok Pythona.

Przejdź do [Lab 11 — Klient zmienił zdanie](../11-changed-requirements/README.pl.md).
