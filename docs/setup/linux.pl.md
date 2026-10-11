# Konfiguracja Linuksa

[In English →](linux.md) · [← Wróć do Zacznij tutaj](../../START-HERE.pl.md)

Ta strona została sprawdzona na Ubuntu i Debianie. W Fedorze, Archu,
openSUSE lub innej dystrybucji użyj odpowiednich poleceń menedżera
pakietów z Części 2. Pozostałe kroki są takie same.

Jeśli gdzieś utkniesz, przejdź do [Jeśli utkniesz](#jeśli-utkniesz) na
dole, albo do wspólnego [`troubleshooting.pl.md`](troubleshooting.pl.md).

## Część 1: Otwórz terminal

1. Otwórz menu aplikacji. Jego nazwa i ikona zależą od środowiska
   graficznego: GNOME używa nazwy **Activities**, a KDE ma własny
   program uruchamiający aplikacje.
2. Wyszukaj **Terminal** (na Ubuntu zwykle nazywa się właśnie
   "Terminal", ikonka wygląda jak ciemny prostokąt z `>_`).
3. Kliknij, żeby go otworzyć.

**Co powinieneś/aś zobaczyć:** okno z promptem tekstowym, zwykle
kończącym się na `$`, coś jak `ty@twojkomputer:~$`.

**Jeśli widzisz coś innego:** jeśli po otwarciu menu aplikacji nie
widzisz pola wyszukiwania, poszukaj ikonki "Terminal" albo "Terminal
Emulator" na pasku zadań albo w doku. Jest dostępny w niemal każdym
środowisku graficznym, choć może mieć inną nazwę.

## Część 2: Sprawdź Gita i zainstaluj, jeśli go nie ma

1. W terminalu sprawdź, czy Git jest już zainstalowany:
   ```bash
   git --version
   ```
2. **Co powinieneś/aś zobaczyć:** coś jak `git version 2.43.0`. Jeśli to
   widzisz, przejdź do Części 3.
3. **Jeśli widzisz** `git: command not found` albo `Command 'git' not
   found`, zainstaluj go. Dokładna komenda zależy od Twojej
   dystrybucji:

   - **Ubuntu/Debian:**
     ```bash
     sudo apt update
     sudo apt install -y git
     ```
   - **Fedora:**
     ```bash
     sudo dnf install -y git
     ```
   - **Arch:**
     ```bash
     sudo pacman -S git
     ```

   Przedrostek `sudo` oznacza, że potrzebne jest hasło administratora.
   Wpisz je i naciśnij Enter. Podczas wpisywania hasło nie pojawi się
   na ekranie.
4. Potwierdź, że zadziałało:
   ```bash
   git --version
   ```
   **Skąd wiesz, że możesz kontynuować:** pojawia się numer wersji
   zamiast błędu.

## Część 3: Zainstaluj VS Code

1. Wejdź na [code.visualstudio.com](https://code.visualstudio.com/) w
   przeglądarce i pobierz pakiet `.deb` (Ubuntu/Debian) albo
   odpowiednik dla swojej dystrybucji.
2. **Ubuntu/Debian**, z terminala, w katalogu, do którego go
   pobrałeś/aś (zwykle `~/Downloads`):
   ```bash
   cd ~/Downloads
   sudo apt install -y ./code_*.deb
   ```
   Dla innych dystrybucji użyj menedżera pakietów swojej dystrybucji z
   pobranym pakietem albo skorzystaj z instrukcji na stronie pobierania.
   Znajdziesz tam polecenie dla swojego formatu pakietu, na przykład
   `.rpm` lub AUR dla Archa.
3. Uruchom VS Code: otwórz menu aplikacji, wyszukaj **Visual Studio
   Code**, kliknij go.

**Co powinieneś/aś zobaczyć:** otwiera się okno VS Code z zakładką
powitalną i pustym panelem Explorer po lewej. Panel jest pusty, ponieważ
nie ma jeszcze otwartego projektu.

## Część 4: Otwórz terminal wewnątrz VS Code

1. W menu VS Code kliknij **Terminal → New Terminal**.
2. **Co powinieneś/aś zobaczyć:** panel terminala otwiera się na dole
   okna, z promptem podobnym do tego z Części 1. VS Code domyślnie
   korzysta z Basha, więc nie musisz niczego konfigurować.
3. Potwierdź:
   ```bash
   pwd
   ```
   **Co powinieneś/aś zobaczyć:** ścieżka, prawdopodobnie kończąca się
   na Twoim katalogu domowym (na przykład `/home/twojanazwa`).

## Część 5: Bieżący katalog i katalog domowy

- `pwd` wyświetla bieżący katalog.
- `ls` wyświetla zawartość bieżącego katalogu.
- `cd` bez argumentów, albo `cd ~`, zawsze zabiera Cię z powrotem do
  katalogu domowego.
- `cd ..` przenosi Cię o jeden poziom wyżej.

Jeśli wynik polecenia wygląda inaczej niż oczekujesz, najpierw uruchom
`pwd`. Wiele problemów wynika z pracy w niewłaściwym katalogu.

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

1. W VS Code kliknij **File → Open Folder…**.
2. Przejdź do sklonowanego katalogu. Jeśli używasz lokalizacji z punktu
   6.3, wybierz `projects` → `software-engineering-in-practice`
   wewnątrz katalogu domowego.
3. Kliknij **OK** (albo **Open**).
4. VS Code może zapytać "Do you trust the authors of the files in this
   folder?" Kliknij **Yes, I trust the authors**.

**Co powinieneś/aś zobaczyć:** panel Explorer po lewej pokazuje `labs`,
`examples`, `scripts`, `README.md` i pozostałe elementy widoczne
wcześniej w wyniku `ls`.

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

- [ ] `git --version` działa w terminalu.
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

### `git` albo `code` mówi "command not found"

Zamknij terminal i otwórz go ponownie zgodnie z Częścią 1 lub 4. Nowo
zainstalowany program może nie być dostępny w oknie otwartym przed
końcem instalacji. Jeśli nadal go brakuje, przewiń terminal w górę i
sprawdź, czy polecenie z Części 2 lub 3 zakończyło się bez błędu.

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
problemów niespecyficznych dla Linuksa (problemy z logowaniem do
GitHuba, "repo już istnieje na dysku", VS Code otworzył niewłaściwy
katalog czy problemy z pierwszym pushem).
