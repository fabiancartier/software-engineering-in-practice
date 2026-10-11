# Konfiguracja Windows

[In English →](windows.md) · [← Wróć do Zacznij tutaj](../../START-HERE.pl.md)

Ten przewodnik pomoże Ci przygotować komputer z Windows 11, pobrać
własną kopię kursu i otworzyć ją w VS Code z terminalem Git Bash.
Wykonuj części po kolei. Każda kończy się krótkim sprawdzeniem, zanim
przejdziesz dalej.

Jeśli pojawi się problem, nie zgaduj. Przejdź do sekcji
[Jeśli utkniesz](#jeśli-utkniesz) na dole tej strony, albo do
wspólnego [`troubleshooting.pl.md`](troubleshooting.pl.md).

## Część 1: Otwórz PowerShell jako administrator

Uprawnienia administratora są potrzebne tylko podczas instalacji w
Częściach 2 i 3. Pozostałe zadania wykonasz na zwykłym koncie.

1. Naciśnij klawisz **Windows** (albo kliknij przycisk Start, w lewym
   dolnym rogu ekranu).
2. Wpisz `powershell`. Windows pokaże **Windows PowerShell** jako
   wynik wyszukiwania.
3. Kliknij **prawym przyciskiem myszy** ten wynik wyszukiwania.
4. Kliknij **Uruchom jako administrator**.
5. Może pojawić się okno z pytaniem "Czy chcesz zezwolić tej aplikacji
   na wprowadzanie zmian na tym urządzeniu?". Kliknij **Tak**.

**Co powinieneś/aś zobaczyć:** okno terminala z niebieskim tłem, w
którego tytule jest **Administrator: Windows PowerShell**. Słowo
"Administrator" w tytule oznacza sesję z podwyższonymi uprawnieniami.
W zwykłym oknie PowerShell tego słowa nie będzie.

**Jeśli widzisz coś innego:** jeśli w tytule okna nie ma słowa
"Administrator", zamknij je i powtórz kroki 1–4. Kliknij wynik prawym
przyciskiem i wybierz **Uruchom jako administrator** zamiast naciskać
Enter.

**Skąd wiesz, że możesz kontynuować:** tytuł okna mówi
**Administrator: Windows PowerShell**.

## Część 2: Zainstaluj Chocolatey

Chocolatey pozwala zainstalować Gita i VS Code jedną komendą każdy,
zamiast szukać instalatorów i klikać przez kreatory konfiguracji.

1. W **administratorskim** oknie PowerShell z Części 1, sprawdź, czy
   jest już zainstalowany:
   ```powershell
   choco --version
   ```
2. **Co powinieneś/aś zobaczyć:** numer wersji (coś jak `2.3.0`).
   Jeśli go widzisz, Chocolatey jest już zainstalowany. Przejdź do
   Części 3.
3. **Jeśli widzisz** `choco: The term 'choco' is not recognized...`,
   Chocolatey jeszcze nie jest zainstalowany. Uruchom oficjalną
   oficjalne polecenie ze strony
   [chocolatey.org/install](https://chocolatey.org/install). Wklej je
   bez zmian:
   ```powershell
   Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
   ```
4. **Co powinieneś/aś zobaczyć:** kilka linii wyniku instalacji,
   kończących się czymś jak `Chocolatey (choco.exe) is now ready`.
5. Zamknij to okno PowerShell całkowicie i otwórz nowe
   **administratorskie** okno PowerShell (powtórz Część 1). Chocolatey
   staje się dostępny tylko w nowych oknach terminala, nie w tym, z
   którego go zainstalowano.
6. Potwierdź, że zadziałało:
   ```powershell
   choco --version
   ```
   **Skąd wiesz, że możesz kontynuować:** wypisuje się numer wersji,
   nie błąd.

**Jeśli nie masz uprawnień administratora na tym komputerze**
(częste na komputerach uczelnianych albo wspólnych): pomiń Chocolatey
całkowicie i przejdź do [Brak uprawnień administratora?](#brak-uprawnień-administratora)
poniżej, a potem wróć tutaj, gdy Git i VS Code będą już zainstalowane
inną metodą.

## Część 3: Zainstaluj Git for Windows i VS Code

Podczas instalacji przez Chocolatey oba programy wymagają uprawnień
administratora. Zainstaluj je kolejno w tym samym oknie PowerShell.

1. Zainstaluj Git:
   ```powershell
   choco install git -y
   ```
   **Co powinieneś/aś zobaczyć:** wynik pobierania i instalacji,
   kończący się czymś jak `git v2.xx.x already installed` albo
   komunikatem sukcesu. Może to zająć minutę lub dwie.
2. Zainstaluj VS Code, w tym samym oknie:
   ```powershell
   choco install vscode -y
   ```
   **Co powinieneś/aś zobaczyć:** wynik instalacji kończący się
   komunikatem sukcesu.
3. Zamknij to **administratorskie** okno PowerShell całkowicie. Nie
   będziesz już potrzebować uprawnień administratora podczas tego
   kursu. Od tego miejsca korzystaj ze zwykłych okien.
4. Otwórz **zwykłe** (nieadministratorskie) okno PowerShell: naciśnij
   klawisz Windows, wpisz `powershell`, tym razem naciśnij **Enter**
   (bez prawego przycisku).
5. Potwierdź, że oba się zainstalowały:
   ```powershell
   git --version
   code --version
   ```
   **Co powinieneś/aś zobaczyć:** `git version 2.xx.x.windows.x`, a
   potem kilka linii informacji o wersji z VS Code.

   **Jeśli którekolwiek mówi "nie jest rozpoznawane"**: zamknij
   *wszystkie* okna PowerShell i terminala, włącznie z VS Code, jeśli
   jest otwarty, i spróbuj jeszcze raz ze świeżo otworzonego okna.
   Instalacja nowego programu czasem nie działa w oknach, które były
   już otwarte wcześniej. Jeśli wciąż nie można go znaleźć, zobacz
   [Jeśli utkniesz](#jeśli-utkniesz).
6. Otwórz sam VS Code po raz pierwszy: naciśnij klawisz Windows, wpisz
   `code`, kliknij **Visual Studio Code**.

**Co powinieneś/aś zobaczyć:** otwiera się okno VS Code, z zakładką
powitalną i pustym panelem Explorer po lewej. Panel jest pusty, ponieważ
nie ma jeszcze otwartego projektu.

**Skąd wiesz, że możesz kontynuować:** `git --version` i `code
--version` wypisują numery wersji, a VS Code się otwiera.

## Część 4: Znajdź Git Bash

Git for Windows zawiera także **Git Bash**. Ten terminal obsługuje
polecenia używane w kursie, takie jak `ls`, `pwd` i `cat`.

1. Naciśnij klawisz **Windows**.
2. Wpisz `git bash`.
3. Kliknij **Git Bash**, gdy się pojawi, żeby go otworzyć.

**Co powinieneś/aś zobaczyć:** okno terminala z czarnym albo ciemnym
tłem i promptem wyglądającym jak `ty@TWÓJKOMPUTER MINGW64 ~`. Właśnie
Napis `MINGW64` oznacza Git Bash, a nie PowerShell lub Wiersz polecenia.

Możesz teraz zamknąć to okno. W Części 5 otworzysz Git Bash wewnątrz
VS Code.

### Czym różnią się PowerShell, Git Bash i terminal VS Code?

- **PowerShell** to terminal systemu Windows. W Częściach 1–3 służył do
  instalacji. Polecenia kursu, takie jak `ls`, `cat` i `grep`, działają
  w nim inaczej, dlatego nie używaj go podczas labów.
- **Git Bash** to terminal w stylu Unix, który przychodzi z Git for
  Windows. Tego terminala dotyczą instrukcje kursu. Gdy lab
  mówi "uruchom to w swoim terminalu", oznacza Git Bash.
- **Terminal VS Code** to po prostu panel terminala *wewnątrz* okna VS
  Code. W Windows domyślnie otwiera PowerShell. W Części 5 zmienisz
  domyślny profil na Git Bash.

## Część 5: Ustaw Git Bash jako domyślny terminal w VS Code

Tę konfigurację wystarczy wykonać raz.

1. W VS Code otwórz menu **Terminal → New Terminal**.
2. **Co powinieneś/aś zobaczyć:** panel terminala otwiera się na dole
   okna. Po nowej instalacji zwykle jest to PowerShell. Za chwilę
   zmienisz ten profil.
3. Kliknij małą **strzałkę rozwijania** przy ikonie `+` w prawym
   górnym rogu panelu terminala. Jeśli nie widzisz tam strzałki
   rozwijania, otwórz Command Palette (**View → Command Palette…**,
   albo `Ctrl+Shift+P`) i wpisz:
   ```text
   Terminal: Select Default Profile
   ```
4. Z listy, która się pojawi, kliknij **Git Bash**.
5. Zamknij panel terminala, który jest aktualnie otwarty (kliknij
   ikonę koszyka, albo kliknij w niego i naciśnij `Ctrl+D`).
6. Otwórz nowy: **Terminal → New Terminal** jeszcze raz.
7. **Co powinieneś/aś zobaczyć:** prompt wyglądający jak
   `ty@TWÓJKOMPUTER MINGW64 ~`, podobny do promptu z Części 4.
8. Potwierdź, że to naprawdę Bash, nie PowerShell w przebraniu:
   ```bash
   echo "$BASH_VERSION"
   ```
   **Skąd wiesz, że możesz kontynuować:** wypisuje się prawdziwy numer
   wersji, na przykład `5.2.26(1)-release`. Pusty wynik lub błąd oznacza,
   że nadal działa PowerShell. W takim przypadku przejdź do następnej
   sekcji.

### Nie widzę Git Bash w VS Code

Jeśli **Git Bash** nigdy nie pojawił się na liście w kroku 4, albo
nowy terminal wciąż jest PowerShellem po przejściu kroków 1–7:

1. **Uruchom ponownie VS Code.** Zamknij wszystkie okna, otwórz
   ponownie i spróbuj Część 5 od kroku 1. VS Code skanuje profile
   terminala podczas uruchamiania i mógł nie zauważyć nowej instalacji.
2. Jeśli wciąż go nie ma, wskaż VS Code na Git Bash ręcznie:
   - Otwórz Command Palette (`Ctrl+Shift+P`) i uruchom
     **Preferences: Open User Settings (JSON)**.
   - Dodaj poniższy fragment do istniejącego obiektu `{ }`. Jeśli nie
     wiesz, jak połączyć ustawienia JSON, przejdź do
     [Jeśli utkniesz](#jeśli-utkniesz):
     ```json
     "terminal.integrated.profiles.windows": {
       "Git Bash": {
         "path": "C:\\Program Files\\Git\\bin\\bash.exe"
       }
     },
     "terminal.integrated.defaultProfile.windows": "Git Bash"
     ```
   - Zapisz plik, potem powtórz kroki 5–8 powyżej.
   - Jeśli Gita nie ma w `C:\Program Files\Git`, znajdź, gdzie
     faktycznie jest. W oknie PowerShell uruchom:
     ```powershell
     (Get-Command git).Source
     ```
     Polecenie wypisze ścieżkę do `git.exe`, zwykle w katalogu `cmd\`,
     na przykład `C:\Program Files\Git\cmd\git.exe`. `bash.exe` znajduje
     się poziom wyżej, w sąsiednim katalogu `bin\`. Jego ścieżkę
     uzyskasz poleceniem:
     ```powershell
     Join-Path (Split-Path (Split-Path (Get-Command git).Source)) "bin\bash.exe"
     ```
     Zanim wkleisz tę ścieżkę do `settings.json`, potwierdź, że plik
     istnieje. Otwórz katalog w Eksploratorze Windows albo uruchom dla
     tej ścieżki `Test-Path`. Wynikiem powinno być `True`.

## Część 6: Zrób fork, sklonuj i otwórz repozytorium

Od tego miejsca będziesz pracować w przeglądarce oraz terminalu Git
Bash wewnątrz VS Code.

**Masz już fork albo lokalną kopię z wcześniejszej próby?** Nie
nie powtarzaj tych kroków. Przejdź najpierw do
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
W adresie i tytule strony powinna znaleźć się Twoja nazwa użytkownika,
a pod nazwą repozytorium informacja o forku
`michalmaj/software-engineering-in-practice`.

**Skąd wiesz, że możesz kontynuować:** URL w adresie przeglądarki
zawiera *Twój* username z GitHuba, nie `michalmaj`.

### 6.2: Skopiuj adres klonowania swojego forka

1. Na stronie swojego forka (z poprzedniego kroku) kliknij zielony
   przycisk **Code**.
2. Upewnij się, że wybrana jest zakładka **HTTPS** (nie SSH, nie
   GitHub CLI). Ten kurs nie wymaga konfigurowania kluczy SSH.
3. Kliknij małą ikonkę kopiowania przy adresie, żeby go skopiować.
   Wygląda jak:
   ```text
   https://github.com/<twój-username>/software-engineering-in-practice.git
   ```
   z Twoim rzeczywistym username zamiast `<twój-username>`.

### 6.3: Sklonuj repozytorium na swój komputer

Z powrotem w terminalu Git Bash wewnątrz VS Code (Część 5):

1. Wybierz katalog na projekty i utwórz go, jeśli jeszcze nie istnieje.
   Możesz użyć poniższej lokalizacji:
   ```bash
   mkdir -p ~/projects
   cd ~/projects
   ```
2. Sklonuj swój fork, używając adresu z punktu 6.2.
   **Zastąp `<twój-username>` swoją nazwą użytkownika z GitHuba**;
   nie wklejaj poniższej linii dosłownie:
   ```bash
   git clone https://github.com/<twój-username>/software-engineering-in-practice.git
   ```
3. **Co powinieneś/aś zobaczyć:** linie jak `Cloning into
   'software-engineering-in-practice'...`, potem `Receiving objects:
   100%`, kończące się bez błędu.
4. Przejdź do nowego folderu:
   ```bash
   cd software-engineering-in-practice
   ```
5. Potwierdź, gdzie jesteś i co tu jest:
   ```bash
   pwd
   ls
   ```
   **Co powinieneś/aś zobaczyć:** `pwd` wypisuje coś kończące się na
   `/projects/software-engineering-in-practice`; `ls` wylistowuje
   foldery włącznie z `labs`, `examples`, `scripts`, i pliki włącznie
   z `README.md` i `START-HERE.md`.
6. Potwierdź, że sam Git jest zadowolony, i że wskazuje na *Twój*
   fork, nie prowadzącego:
   ```bash
   git status
   git remote -v
   ```
   **Co powinieneś/aś zobaczyć:** `git status` mówi `On branch main`
   i `nothing to commit, working tree clean`. `git remote -v` pokazuje
   dwie linie (`origin` fetch i push), których adres zawiera **Twój
   własną nazwę użytkownika z GitHuba**. Jeśli widzisz `michalmaj`, masz
   klon oryginalnego repozytorium zamiast swojego forka. Zobacz
   [Sklonowałem/am repozytorium prowadzącego zamiast własnego forka](#sklonowałemam-repozytorium-prowadzącego-zamiast-własnego-forka)
   poniżej.

**Skąd wiesz, że możesz kontynuować:** `git remote -v` pokazuje Twój
własny username, a `ls` pokazuje `labs/`, `examples/` i `scripts/`.

### 6.4: Otwórz repozytorium w VS Code

1. W VS Code otwórz menu **File → Open Folder…**
2. Przejdź do sklonowanego katalogu. Jeśli używasz lokalizacji z
   punktu 6.3, wybierz `projects` → `software-engineering-in-practice`
   wewnątrz Twojego folderu użytkownika Windows.
3. Kliknij **Select Folder**.
4. VS Code może zapytać "Do you trust the authors of the files in
   this folder?". Kliknij **Yes, I trust the authors**.

**Co powinieneś/aś zobaczyć:** panel Explorer po lewej wylistowuje
teraz `labs`, `examples`, `scripts`, `README.md` i pozostałe elementy
widoczne wcześniej w wyniku `ls`.

5. Otwórz terminal wewnątrz tego okna (**Terminal → New Terminal**).
   powinien już domyślnie być Git Bash, bo ustawiłeś/aś to w Części 5.
   Potwierdź, że jesteś we właściwym miejscu:
   ```bash
   pwd
   ```
   **Skąd wiesz, że możesz kontynuować:** ścieżka kończy się na
   `/software-engineering-in-practice`, a panel Explorer pokazuje
   folder `labs/`.

### 6.5: Znajdź Lab 01

1. W panelu Explorer po lewej kliknij, żeby rozwinąć folder `labs`.
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

- [ ] `git --version` działa w zwykłym terminalu.
- [ ] Nowy terminal w VS Code domyślnie otwiera Git Bash
      (`echo "$BASH_VERSION"` wypisuje wersję).
- [ ] `pwd` wewnątrz terminala VS Code kończy się na
      `/software-engineering-in-practice`.
- [ ] `git status` działa bez błędu.
- [ ] `git remote -v` pokazuje **Twój własny** username z GitHuba.
- [ ] Panel Explorer pokazuje `labs/`, `examples/` i `scripts/`.
- [ ] `labs/01-workstation/README.pl.md` się otwiera.

Wszystko zaznaczone? Otwórz
[`labs/01-workstation/README.pl.md`](../../labs/01-workstation/README.pl.md)
i zaczynaj.

## Brak uprawnień administratora?

Jeśli Części 1–2 nie są możliwe na tym komputerze (częste na
komputerach uczelnianych), możesz wciąż zainstalować wszystko przez
oficjalne instalatory zamiast Chocolatey. Niektóre pozwalają
zainstalować program w katalogu użytkownika bez podwyższonych
uprawnień:

- **Git for Windows**: pobierz instalator ze strony
  [git-scm.com/download/win](https://git-scm.com/download/win).
  Uruchom go; gdy kreator konfiguracji zapyta, gdzie zainstalować,
  zwykle możesz wskazać folder wewnątrz własnego profilu użytkownika
  zamiast `C:\Program Files`, jeśli nie masz tam prawa zapisu.
  Zachowaj każdą inną domyślną opcję bez zmian.
- **VS Code**: pobierz **User Installer** (nie "System Installer") ze
  strony [code.visualstudio.com](https://code.visualstudio.com/).
  User Installer instaluje program w
  własnego folderu użytkownika bez potrzeby uprawnień administratora.

Po zainstalowaniu obu programów wróć do Części 4. Dalsze kroki są takie
same niezależnie od metody instalacji.

Jeśli żaden instalator nie może się uruchomić na tym komputerze w
ogóle, użyj środowiska zdalnego. Zobacz
[GitHub Codespaces](../../START-HERE.pl.md#github-codespaces-opcja-bez-instalacji)
w Zacznij tutaj.

## Jeśli utkniesz

### `choco`, `git` albo `code` mówi "not recognized"

Zamknij wszystkie okna terminala oraz VS Code i otwórz je ponownie. Nowo
zainstalowany program często nie pojawia się w oknach, które były już
otwarte przed zakończeniem instalacji. Jeśli wciąż go nie ma po tym,
sprawdź, czy odpowiednia część instalacji zakończyła się bez błędu.

### Sklonowałem/am repozytorium prowadzącego zamiast własnego forka

Jeśli `git remote -v` pokazuje `michalmaj` zamiast Twojego username:
folder, który sklonowałeś/aś, wskazuje na repozytorium, do którego nie
masz prawa push, co zablokuje Cię później (Lab 03–04). Napraw to bez
utraty czegokolwiek:

```bash
cd ..
mv software-engineering-in-practice software-engineering-in-practice.instructor-copy
```

To zmienia nazwę źle sklonowanego folderu, odsuwając go z drogi,
zamiast go usuwać, potem powtórz 6.1–6.4 tym razem z adresem *swojego*
forka. Gdy poprawna kopia działa, możesz usunąć przemianowany katalog
albo pozostawić go na dysku.

### Wszystko inne

Zobacz wspólne [`troubleshooting.pl.md`](troubleshooting.pl.md) dla
problemów niespecyficznych dla Windows (problemy z logowaniem do
GitHuba, "repo już istnieje na dysku", niewłaściwy folder otwarty w VS
Code, problemy z pierwszym push i więcej).
