# Lab 05 — "Działa na moim komputerze"

## Sytuacja

Ktoś z zespołu przesyła Ci mały projekt i mówi: „Po prostu go uruchom”.
Na jego komputerze wszystko działa. Na Twoim brakuje zależności albo
wersja któregoś narzędzia jest niewłaściwa. Projekt nie określa swoich
wymagań, więc przyczyny można się tylko domyślać.

Starter znajduje się w `examples/works-on-my-machine/<Twój język>/`.
Od tego miejsca wszystkie projekty używane w kolejnych labach będą w
`examples/`. Wybierz też jedną z trzech ścieżek: Python, Go albo Java.
Skorzystaj z języka używanego przez zespół lub wybierz go samodzielnie,
jeśli pracujesz bez zespołu, i wykonaj odpowiednią sekcję poniżej.

## Cele nauki

Po tym labie potrafisz:

- Wyjaśnić, dlaczego "u mnie działa" nie jest dowodem na to, że program
  jest poprawnie przygotowany ani odtwarzalny.
- Użyć narzędzi swojej ścieżki do jawnego określenia i egzekwowania
  zależności projektu lub wymaganej wersji narzędzia.
- Wyjaśnić rolę manifestu projektu i zachowanie narzędzi, gdy na
  komputerze brakuje jednego z wymagań.
- Ogólnie wyjaśnić, co konfiguracja devcontainera zapewnia dla każdego
  z trzech języków.

## Zanim zaczniesz

### Python

- Lab 04 ukończony.
- Wszystkie poniższe polecenia uruchamiaj w
  `examples/works-on-my-machine/python/`, chyba że instrukcja mówi
  inaczej.
- Zainstalowane `uv`. Jeśli jesteś w Codespace/devcontainerze tego
  repozytorium, jest już gotowe (patrz główny
  [`README.pl.md`](../../README.pl.md)). Jeśli jeszcze go nie masz,
  zainstaluj poleceniem:
  `curl -LsSf https://astral.sh/uv/0.11.21/install.sh | sh` (przypięte
  do tej samej wersji co devcontainer).

### Go

- Lab 04 ukończony.
- Wszystkie poniższe polecenia uruchamiaj w
  `examples/works-on-my-machine/go/`, chyba że instrukcja mówi inaczej.
- Zainstalowane Go 1.27.x (patrz tabela wersji narzędzi w głównym
  README). W Codespace lub devcontainerze repozytorium jest już
  dostępne.

### Java

- Lab 04 ukończony.
- Wszystkie poniższe polecenia uruchamiaj w
  `examples/works-on-my-machine/java/`, chyba że instrukcja mówi
  inaczej.
- Zainstalowany JDK 21 (patrz tabela toolchainu w głównym README).
  Globalna instalacja Gradle nie jest potrzebna, ponieważ starter
  zawiera Gradle Wrapper.

## Twoje zadanie

### Python

1. Bez instalowania czegokolwiek uruchom `python3 main.py` (na
   Windows w Git Bash użyj `python main.py`; oficjalny instalator
   Pythona dla Windows dostarcza polecenie `python`, nie `python3`).
   Przeczytaj komunikat błędu. `command not found` oznacza, że brakuje
   nawet interpretera Pythona, nie tylko zależności projektu. W obu
   przypadkach przejdź dalej; krok 4 rozwiąże problem.
2. Otwórz `pyproject.toml` i sprawdź, od jakiego pakietu zależy projekt.
3. Uruchom `uv sync`. Zobacz, co pojawiło się w tym katalogu.
4. Uruchom `uv run python main.py`. Porównaj ten wynik z krokiem 1.
5. Uruchom `uv run pytest` i potwierdź, że testy przechodzą.
6. Z powrotem w katalogu głównym repozytorium, w nowym pliku
   `labs/05-works-on-my-machine/notes/my-observations.txt` zapisz
   własnymi słowami, dlaczego krok 1 się nie powiódł, co utworzył
   `uv sync` i po co oraz co stałoby się z osobą, która uruchomiłaby
   tylko `python3 main.py` bez wcześniejszego `uv sync`.
7. Otwórz `.devcontainer/devcontainer.json` w katalogu głównym
   repozytorium i znajdź linię, która dostarcza Pythona. Dopisz do
   notatek jeszcze jedno zdanie wyjaśniające, co w tym samym pliku
   zapewnia Go i Javę.

### Go

1. Potwierdź, że starter działa w wyjściowym stanie:
   ```bash
   go test ./...
   go run .
   ```
   Oczekiwane: `ok` i `It works on my machine!`.
2. W swojej kopii roboczej otwórz `go.mod` i zmień wersję w linii `go`
   na następną wersję minor. Zmień `go 1.27` na `go 1.28`, aby
   zasymulować projekt wymagający nowszego Go niż wersja dostępna w
   ściśle skonfigurowanym środowisku.
3. Uruchom `GOTOOLCHAIN=local go build ./...`. Przeczytaj błąd. Jest
   zgodny z rzeczywistym zachowaniem Go. `GOTOOLCHAIN=local` zezwala
   jedynie na użycie toolchaina zainstalowanego lokalnie. Tak może być
   celowo skonfigurowane środowisko bez dostępu do sieci lub CI z
   ograniczonymi uprawnieniami.
4. Uruchom ten sam build jeszcze raz, *bez* `GOTOOLCHAIN=local`, czyli
   z domyślnym ustawieniem Go (`auto`): `go build ./...`.
   Zobaczysz jedną z dwóch rzeczy, zależnie od tego, czy Go 1.28 było
   już wydane: narzędzie zostanie pobrane i build przejdzie albo pojawi
   się błąd `toolchain not available`. To inny przypadek niż w kroku 3.
   Ustawienie `auto` próbuje pobrać wymaganą wersję, ale nie może pobrać
   wydania, które jeszcze nie istnieje. Ustawienie `local` w ogóle nie
   podejmuje próby pobrania.
5. Przywróć w `go.mod` zacommitowaną wartość `go 1.27`. Ponownie uruchom
   `go build ./...` z domyślnym `auto`, bez zmiennej środowiskowej.
   Polecenie przejdzie, ponieważ `1.27` jest wydaną wersją, którą Go
   może pobrać, jeśli nie ma jej lokalnie. `auto` zapewnia toolchain
   wymagany przez `go.mod`, a `local` ogranicza się do już
   zainstalowanych wersji.
6. Potwierdź, że `go test ./...` znowu przechodzi na przywróconym,
   zacommitowanym stanie. Taki stan sprawdza Course Health.
7. Z powrotem w katalogu głównym repozytorium, w nowym pliku
   `labs/05-works-on-my-machine/notes/my-observations.txt` zapisz
   własnymi słowami, czego odmówił `GOTOOLCHAIN=local` w kroku 3,
   dlaczego krok 4 nie powiódł się z innego powodu oraz co stałoby się
   z osobą mającą tylko Go 1.27, gdyby zacommitowany `go.mod` wymagał
   wersji 1.28.
8. Otwórz `.devcontainer/devcontainer.json` w katalogu głównym
   repozytorium i znajdź linię, która dostarcza Go. Dopisz do swojego
   pliku notatek jeszcze jedno zdanie wyjaśniające, co w tym samym
   pliku zapewnia Pythona i Javę.

### Java

1. Potwierdź, że starter działa w wyjściowym stanie:
   ```bash
   ./gradlew test build
   ```
   Oczekiwane: `BUILD SUCCESSFUL`.
2. W swojej własnej kopii roboczej otwórz `build.gradle` i znajdź
   `JavaLanguageVersion.of(21)`. Zmień `21` na `25`.
3. Uruchom `./gradlew build` jeszcze raz. Komunikat wyjaśnia, że żadna
   zainstalowana wersja JDK nie spełnia nowego wymagania, a Gradle nie
   jest skonfigurowany do jej pobrania.
4. Zmień `25` z powrotem na `21`. Uruchom `./gradlew build` jeszcze raz
   i potwierdź, że ponownie kończy się sukcesem.
5. Z powrotem w katalogu głównym repozytorium, w nowym pliku
   `labs/05-works-on-my-machine/notes/my-observations.txt` zapisz
   własnymi słowami, czego odmówił Gradle w kroku 3 i dlaczego, gdzie
   projekt deklaruje wymaganą wersję Javy oraz co stałoby się z osobą,
   która próbowałaby zbudować ten projekt z JDK 17 zamiast 21.
6. Otwórz `.devcontainer/devcontainer.json` w katalogu głównym
   repozytorium i znajdź linię, która dostarcza Javę. Dopisz do
   swojego pliku notatek jeszcze jedno zdanie wyjaśniające, co w tym
   samym pliku zapewnia Pythona i Go.

## Kryteria akceptacji

### Python

- `uv run pytest` przechodzi wewnątrz
  `examples/works-on-my-machine/python/`.
- `.venv/` i `uv.lock` istnieją w tym katalogu (uv je utworzył; nie
  pisz żadnego z nich ręcznie).
- `uv.lock`, utworzony przez `uv sync` (nie dostarczany razem ze
  starterem), jest zacommitowany do repozytorium. Plik blokady przydaje
  się zespołowi tylko wtedy, gdy znajduje się w repozytorium.
- `labs/05-works-on-my-machine/notes/my-observations.txt` odpowiada na
  wszystkie trzy punkty z kroku 6, plus na pytanie o devcontainer z
  kroku 7.

### Go

- `go test ./...` i `go run .` przechodzą po przywróceniu `go.mod` do
  `go 1.27`. Taki stan
  sprawdza Course Health.
- `go.mod` jest zacommitowany bez zmian względem startera (`go 1.27`);
  podniesienie do `1.28` istniało tylko w Twojej własnej kopii
  roboczej na czas eksperymentu, nigdy niezacommitowane.
- `labs/05-works-on-my-machine/notes/my-observations.txt` odpowiada na
  wszystkie trzy punkty z kroku 7, plus na pytanie o devcontainer z
  kroku 8.

### Java

- `./gradlew test build` przechodzi po przywróceniu
  `JavaLanguageVersion.of(21)`. Taki stan sprawdza Course Health.
- `build.gradle` jest zacommitowany bez zmian względem startera
  (`JavaLanguageVersion.of(21)`); zmiana na `25` istniała tylko w
  Twojej własnej kopii roboczej na czas eksperymentu, nigdy
  niezacommitowana.
- `labs/05-works-on-my-machine/notes/my-observations.txt` odpowiada na
  wszystkie trzy punkty z kroku 5, plus na pytanie o devcontainer z
  kroku 6.

## Weryfikacja

### Python

```bash
cd examples/works-on-my-machine/python
uv run pytest
test -f uv.lock && echo "lock file exists"
test -d .venv && echo "virtualenv exists"
cd -
test -f labs/05-works-on-my-machine/notes/my-observations.txt && echo "notes exist"
```

### Go

```bash
cd examples/works-on-my-machine/go
cat go.mod
go test ./...
go run .
cd -
test -f labs/05-works-on-my-machine/notes/my-observations.txt && echo "notes exist"
```

Oczekiwany wynik: `go.mod` zawiera `go 1.27`. Jeśli nadal widzisz
`1.28`, przywróć właściwą wersję. `go test ./...` wyświetla `ok`, a
`go run .` wyświetla `It works on my machine!`.

### Java

```bash
cd examples/works-on-my-machine/java
grep JavaLanguageVersion build.gradle
./gradlew test build
cd -
test -f labs/05-works-on-my-machine/notes/my-observations.txt && echo "notes exist"
```

Oczekiwany wynik: linia z `grep` zawiera
`JavaLanguageVersion.of(21)`. Jeśli nadal widzisz `25`, przywróć
właściwą wersję. `./gradlew test build` kończy się `BUILD SUCCESSFUL`.

## Zastanów się

- Manifest Twojej ścieżki określa potrzeby projektu, a plik blokady lub
  przypięta wersja toolchaina wskazuje konkretną wersję, która je
  spełnia. Dlaczego potrzebne są oba?
- Jeśli dwie osoby z zespołu na różnych systemach operacyjnych
  zbuduje ten sam projekt z tego samego zacommitowanego manifestu, czy
  powinni skończyć ze zgodnymi wynikami? Dlaczego?
- Konfiguracja devcontainer dostarcza wszystkie trzy języki systemowo,
  ale ćwiczenie dotyczy wymagań na poziomie
  projektu (zależności Pythona, wersji toolchaina Go, wersji języka
  Javy). Jaka jest różnica między "środowisko uruchomieniowe języka
  jest dostępne na tym komputerze" a "wymagania tego projektu są
  odtwarzalne"?

## Jeśli utkniesz

### Python

- **Podpowiedź 1:** Całe laboratorium to trzy polecenia: `uv sync`, `uv
  run python main.py`, `uv run pytest`. Reszta to czytanie i pisanie
  notatek.
- **Podpowiedź 2:** Jeśli `python3 main.py` (albo `python main.py` na
  Windows) "po prostu działa" u Ciebie bez `uv sync`, to dlatego, że
  `cowsay` jest już zainstalowany globalnie. To pułapka omawiana w tym
  labie. Uruchom polecenie w nowym Codespace, aby zobaczyć błąd.
- **Podpowiedź 3:** `uv run <command>` uruchamia `<command>` wewnątrz
  środowiska zarządzanego przez sam projekt, bez potrzeby ręcznej
  aktywacji czegokolwiek.

### Go

- **Podpowiedź 1:** Jeśli błąd z kroku 3 w ogóle nie wspomina `1.28`,
  sprawdź, czy plik `go.mod` został zapisany po edycji. `go build`
  czyta go na nowo przy każdym uruchomieniu i nie pamięta starego
  wymagania.
- **Podpowiedź 2:** `GOTOOLCHAIN` to standardowa zmienna środowiskowa
  obsługiwana przez Go. `go env GOTOOLCHAIN` pokazuje domyślne
  ustawienie komputera, zwykle `auto`, gdy nie nadpisujesz go w linii
  poleceń.
- **Podpowiedź 3:** Jeśli nie pamiętasz, czy przywróciłeś/aś `go.mod`,
  sprawdź go poleceniem `cat go.mod`. Powinien zawierać `go 1.27`, a
  `git diff go.mod` nie powinien pokazać zmian.

### Java

- **Podpowiedź 1:** Jeśli `./gradlew build` wydaje się wisieć albo nic
  nie robić po zmianie wersji Javy, zaczekaj kilka sekund. Gradle
  sprawdza zainstalowane JDK, zanim będzie mógł
  powiedzieć, że żaden nie pasuje.
- **Podpowiedź 2:** Błąd podaje dokładne wymaganie, którego nie udało
  się spełnić (`languageVersion=25`). Wartość pochodzi z linii
  `JavaLanguageVersion.of(...)` w `build.gradle`, a nie z ogólnego
  błędu Gradle.
- **Podpowiedź 3:** Jeśli nie pamiętasz, czy przywróciłeś/aś
  `build.gradle`, uruchom `git diff build.gradle`. Polecenie nie powinno
  pokazać żadnych zmian.

Zanim przejdziesz dalej, zacommituj i wypchnij wszystkie zmiany z tego
laba, w tym wygenerowany w ścieżce Pythona plik `uv.lock`. Manifesty Go
i Javy powinny pozostać bez zmian; w tych ścieżkach nie ma dodatkowego
pliku blokady. Użyj
`git add -A && git commit -m "..."; git push`. Kolejne laby nie wymagają
jeszcze czystego drzewa, ale Akt IV, zaczynający się od Labu 16, już tak.

## Co dalej

Masz już mały, odtwarzalny projekt w wybranym języku. Projekty rzadko
pozostają jednak w jednym pliku. W następnym labie uporządkujesz skrypt,
który przestał mieścić się w tej prostej formie.

Przejdź do [Lab 06 — Od skryptu do projektu](../06-from-script-to-project/README.pl.md).
