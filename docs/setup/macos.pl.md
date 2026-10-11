# Konfiguracja macOS

[In English →](macos.md) · [← Wróć do Zacznij tutaj](../../START-HERE.pl.md)

Jeśli gdzieś utkniesz, przejdź do [Jeśli utkniesz](#jeśli-utkniesz) na
dole, albo do wspólnego [`troubleshooting.pl.md`](troubleshooting.pl.md).

## Część 1: Otwórz Terminal

1. Naciśnij `Cmd+Space`, żeby otworzyć wyszukiwanie Spotlight.
2. Wpisz `Terminal`.
3. Naciśnij Enter, albo kliknij **Terminal**, gdy się pojawi.

**Co powinieneś/aś zobaczyć:** okno z promptem tekstowym, coś jak
`twojanazwa@Twoj-MacBook ~ %`.

**Co zrobić, jeśli widzisz coś innego:** jeśli Spotlight nie znajduje
Terminala, jest też w **Applications → Utilities → Terminal** przez
Findera.

## Część 2: Sprawdź Xcode Command Line Tools

Git na macOS przychodzi razem z narzędziami wiersza poleceń Apple, nie
jest instalowany osobno.

1. W Terminalu:
   ```bash
   git --version
   ```
2. **Możliwe są dwa wyniki:**
   - Numer wersji, na przykład `git version 2.39.3 (Apple Git-145)`.
     Narzędzia są już zainstalowane, więc przejdź do Części 3.
   - Okno popup: "The 'git' command requires the command line developer
     tools. Would you like to install the tools now?". Kliknij
     **Install**, potem zaakceptuj umowę licencyjną, która się pojawi.
3. Jeśli widziałeś/aś popup, zaczekaj, aż pobieranie i instalacja się
   zakończą (to może zająć kilka minut, zależnie od połączenia), potem
   potwierdź:
   ```bash
   git --version
   ```
   **Skąd wiesz, że możesz kontynuować:** pojawia się numer
   wersji.

## Część 3: Zainstaluj VS Code

Najprościej użyć Homebrew, konsolowego menedżera
pakietów macOS. Jeśli nie chcesz instalować Homebrew, użyj alternatywy
z bezpośrednim pobraniem poniżej.

### Opcja A: Homebrew (polecana)

1. Sprawdź, czy Homebrew jest już zainstalowany:
   ```bash
   brew --version
   ```
2. **Jeśli widzisz** `command not found: brew`, zainstaluj go oficjalną
   poleceniem ze strony [brew.sh](https://brew.sh). Wklej je bez
   zmian:
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```
3. Postępuj zgodnie z instrukcjami wyświetlonymi na końcu. Na Makach
   z Apple Silicon instalator Homebrew zwykle prosi o uruchomienie
   jednej lub dwóch dodatkowych komend, żeby dodać go do `PATH` Twojej
   powłoki. Skopiuj i uruchom wyświetlone polecenie.
4. Zamknij Terminal całkowicie i otwórz go ponownie (Część 1), potem
   potwierdź:
   ```bash
   brew --version
   ```
   **Skąd wiesz, że możesz kontynuować:** pojawia się numer
   wersji.
5. Zainstaluj VS Code:
   ```bash
   brew install --cask visual-studio-code
   ```
6. **Co powinieneś/aś zobaczyć:** wynik pobierania i instalacji
   kończący się bez błędu.

### Opcja B: Bezpośrednie pobranie

1. Wejdź na [code.visualstudio.com](https://code.visualstudio.com/) w
   przeglądarce i pobierz wersję dla macOS.
2. Otwórz pobrany plik `.zip`. Po rozpakowaniu otrzymasz
   `Visual Studio Code.app`.
3. Przenieś go do katalogu **Applications**.

### Uruchom VS Code

Naciśnij `Cmd+Space`, wpisz `Visual Studio Code`, naciśnij Enter.

**Co powinieneś/aś zobaczyć:** otwiera się okno VS Code z zakładką
powitalną i pustym panelem Explorer po lewej. Panel jest pusty, ponieważ
nie ma jeszcze otwartego projektu.

## Część 4: Otwórz terminal wewnątrz VS Code

1. W menu VS Code kliknij **Terminal → New Terminal**.
2. **Co powinieneś/aś zobaczyć:** panel terminala otwiera się na dole
   okna, z promptem podobnym do tego z Części 1. VS Code domyślnie
   korzysta z zsh, więc nie musisz niczego konfigurować.
3. Potwierdź:
   ```bash
   pwd
   ```
   **Co powinieneś/aś zobaczyć:** ścieżka, prawdopodobnie kończąca się
   na Twoim katalogu domowym (na przykład `/Users/twojanazwa`).

## Część 5: Bieżący katalog i katalog domowy

- `pwd` wyświetla bieżący katalog.
- `ls` wyświetla zawartość bieżącego katalogu.
- `cd` bez argumentów, albo `cd ~`, zawsze zabiera Cię z powrotem do
  katalogu domowego.
- `cd ..` przenosi Cię o jeden poziom wyżej.

Jeśli w jakimkolwiek momencie wynik komendy wygląda nieznajomo, najpierw
uruchom `pwd`. Wiele problemów wynika z pracy w innym katalogu niż
oczekiwany.

## Część 6: Zrób fork, sklonuj i otwórz repozytorium

To jest faktyczny cel tej strony. Od teraz wszystko dzieje się albo w
przeglądarce, albo w terminalu VS Code z Części 4.

**Masz już fork albo lokalną kopię z wcześniejszej próby?** Nie
powtarzaj tych kroków. Przejdź najpierw do
[`already-have-a-fork.pl.md`](already-have-a-fork.pl.md).

### 6.1: Zrób fork repozytorium na GitHubie

1. Upewnij się, że konto GitHub jest zalogowane. Otwórz
   [github.com](https://github.com) w przeglądarce i sprawdź, czy
   prawy górny róg pokazuje Twój avatar konta, nie przycisk "Sign in".
   Jeśli nie masz jeszcze konta GitHub, najpierw je utwórz (jest
   darmowe), zanim przejdziesz dalej.
2. Przejdź do
   `https://github.com/michalmaj/software-engineering-in-practice`.
3. Kliknij przycisk **Fork**, w prawym górnym rogu strony.
4. Na stronie "Create a new fork" upewnij się, że **Owner** pokazuje
   *Twoje* konto, a nie konto prowadzącego.
5. Kliknij **Create fork**.

**Co powinieneś/aś zobaczyć:** GitHub przenosi Cię na nową stronę,
której URL to
`https://github.com/<twój-username>/software-engineering-in-practice`.
W adresie i tytule strony powinna znaleźć się Twoja nazwa użytkownika.
GitHub pokazuje też, że repozytorium jest forkiem
`michalmaj/software-engineering-in-practice`.

**Skąd wiesz, że możesz kontynuować:** adres w przeglądarce zawiera
*Twoją* nazwę użytkownika GitHuba, a nie `michalmaj`.

### 6.2: Skopiuj adres klonowania swojego forka

1. Na stronie swojego forka (z poprzedniego kroku) kliknij zielony
   przycisk **Code**.
2. Upewnij się, że wybrana jest zakładka **HTTPS**, a nie SSH lub GitHub
   CLI. Ten kurs nie wymaga konfigurowania kluczy SSH.
3. Kliknij małą ikonkę kopiowania przy adresie, żeby go skopiować.
   Wygląda jak:
   ```text
   https://github.com/<twój-username>/software-engineering-in-practice.git
   ```
   z Twoją nazwą użytkownika zamiast `<twój-username>`.

### 6.3: Sklonuj je na swój komputer

Z powrotem w terminalu VS Code z Części 4:

1. Wybierz katalog na swoje projekty i utwórz go, jeśli jeszcze nie
   istnieje. Możesz użyć na przykład takiej lokalizacji:
   ```bash
   mkdir -p ~/projects
   cd ~/projects
   ```
2. Sklonuj swój fork przy użyciu adresu z punktu 6.2.
   **Zastąp `<twój-username>` swoją nazwą użytkownika z GitHuba**;
   nie wklejaj poniższej linii dosłownie:
   ```bash
   git clone https://github.com/<twój-username>/software-engineering-in-practice.git
   ```
3. **Co powinieneś/aś zobaczyć:** linie jak `Cloning into
   'software-engineering-in-practice'...`, potem `Receiving objects:
   100%`, kończące się bez błędu.
4. Przejdź do nowego katalogu:
   ```bash
   cd software-engineering-in-practice
   ```
5. Potwierdź, gdzie jesteś i co tu jest:
   ```bash
   pwd
   ls
   ```
   **Co powinieneś/aś zobaczyć:** `pwd` wypisuje coś kończące się na
   `/projects/software-engineering-in-practice`; `ls` pokazuje między
   innymi katalogi `labs`, `examples`, `scripts` oraz pliki `README.md`
   i `START-HERE.md`.
6. Potwierdź, że sam Git jest zadowolony, i że wskazuje na *Twój* fork,
   nie prowadzącego:
   ```bash
   git status
   git remote -v
   ```
   **Co powinieneś/aś zobaczyć:** `git status` mówi `On branch main` i
   `nothing to commit, working tree clean`. `git remote -v` pokazuje
   dwie linie (`origin` fetch i push), których adres zawiera **Twój
   własną nazwę użytkownika z GitHuba**. Jeśli widzisz `michalmaj`, masz
   klon oryginalnego repozytorium zamiast swojego forka. Zobacz
   [Sklonowałem/am repozytorium prowadzącego zamiast własnego forka](#sklonowałemam-repozytorium-prowadzącego-zamiast-własnego-forka)
   poniżej.

**Skąd wiesz, że możesz kontynuować:** `git remote -v` pokazuje Twój
własny login, a `ls` pokazuje `labs/`, `examples/` i `scripts/`.

### 6.4: Otwórz repozytorium w VS Code

1. W VS Code kliknij **File → Open Folder…** (albo **File → Open…** w
   niektórych wersjach VS Code. Nadal otwiera okno wyboru katalogu na
   macOS).
2. Przejdź do sklonowanego katalogu. Jeśli używasz lokalizacji z punktu
   6.3, wybierz `projects` → `software-engineering-in-practice`
   wewnątrz katalogu domowego.
3. Kliknij **Open**.
4. VS Code może zapytać "Do you trust the authors of the files in this
   folder?" Kliknij **Yes, I trust the authors**.

**Co powinieneś/aś zobaczyć:** panel Explorer po lewej wylistowuje
teraz `labs`, `examples`, `scripts`, `README.md` i pozostałe elementy
widoczne wcześniej w wyniku `ls`.

5. Otwórz terminal wewnątrz tego okna (**Terminal → New Terminal**).
   Potwierdź, że jesteś we właściwym miejscu:
   ```bash
   pwd
   ```
   **Skąd wiesz, że możesz kontynuować:** ścieżka kończy się na
   `/software-engineering-in-practice`, a panel Explorer pokazuje
   katalog `labs/`.

### 6.5: Znajdź Lab 01

1. W panelu Explorer po lewej rozwiń katalog `labs`.
2. Znajdź i kliknij `01-workstation`, potem kliknij `README.pl.md`
   wewnątrz niego.

**Co powinieneś/aś zobaczyć:** otwiera się `Lab 01 — Witaj na swoim
stanowisku pracy` jako czytelny dokument w edytorze.

Konfiguracja jest gotowa. Przejdź do sekcji
[Zanim skończysz](#zanim-skończysz), sprawdź wszystkie punkty i zacznij
lab.

## Pierwszy push do GitHuba

Pierwszy push pojawi się dopiero w Labie 03 lub 04. Klonowanie i
czytanie repozytorium nie wymaga uwierzytelnienia Gita. Gdy `git push`
poprosi o zalogowanie, skorzystaj z
[`github-auth.pl.md`](github-auth.pl.md).

## Zanim skończysz

Sprawdź każde z tych, zanim uznasz Lab 01 za gotowy do zaczęcia:

- [ ] `git --version` działa w Terminalu.
- [ ] Terminal VS Code otwiera się i działa tak jak terminal z Części 1.
- [ ] `pwd` wewnątrz terminala VS Code kończy się na
      `/software-engineering-in-practice`.
- [ ] `git status` działa bez błędu.
- [ ] `git remote -v` pokazuje **Twoją** nazwę użytkownika GitHuba.
- [ ] Panel Explorer pokazuje `labs/`, `examples/` i `scripts/`.
- [ ] `labs/01-workstation/README.pl.md` się otwiera.

Wszystko zaznaczone? Otwórz
[`labs/01-workstation/README.pl.md`](../../labs/01-workstation/README.pl.md)
i zaczynaj.

## Jeśli utkniesz

### `brew`, `git` albo `code` mówi "command not found"

Zamknij Terminal i otwórz go ponownie zgodnie z Częścią 1. Nowo zainstalowany
program, i własna konfiguracja `PATH` Homebrew, czasem nie zadziałają w
oknie Terminala, które było już otwarte przed zakończeniem instalacji.
Jeśli `brew` wciąż go nie ma po tym, sprawdź ponownie koniec wyniku
instalacji Homebrew (Część 3) pod kątem dodatkowej komendy konfiguracji
`PATH`, o którą poprosił. Ten krok łatwo przeoczyć.

### Sklonowałem/am repozytorium prowadzącego zamiast własnego forka

Jeśli `git remote -v` pokazuje `michalmaj` zamiast Twojej nazwy
użytkownika, sklonowany katalog wskazuje na repozytorium, do którego nie
masz prawa push, co zablokuje Cię później (Lab 03–04). Napraw to bez
utraty czegokolwiek:

```bash
cd ..
mv software-engineering-in-practice software-engineering-in-practice.instructor-copy
```

W ten sposób zachowasz błędnie sklonowany katalog pod inną nazwą.
Następnie powtórz punkty 6.1–6.4, tym razem z adresem *swojego* forka.
Gdy poprawna kopia działa, możesz usunąć przemianowany katalog albo
pozostawić go na dysku.

### Wszystko inne

Zobacz wspólne [`troubleshooting.pl.md`](troubleshooting.pl.md) dla
problemów niespecyficznych dla macOS (problemy z logowaniem do
GitHuba, "repo już istnieje na dysku", VS Code otworzył niewłaściwy
katalog czy problemy z pierwszym pushem).
