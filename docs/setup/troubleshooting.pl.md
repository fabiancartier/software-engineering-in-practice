# Rozwiązywanie problemów

[In English →](troubleshooting.md) · [← Wróć do Zacznij tutaj](../../START-HERE.pl.md)

Znajdź poniżej nagłówek opisujący problem, który widzisz. Te wskazówki
dotyczą wszystkich systemów operacyjnych.

## Nie mam uprawnień administratora na tym komputerze

Zobacz sekcję
[Brak uprawnień administratora?](windows.pl.md#brak-uprawnień-administratora)
w przewodniku dla Windows. Opisuje ona instalację Gita i VS Code bez
uprawnień administratora. Jeśli ta metoda również jest zablokowana,
użyj
[GitHub Codespaces](../../START-HERE.pl.md#github-codespaces-opcja-bez-instalacji)
bez instalowania narzędzi lokalnie.

## `choco`, `git`, `code` albo `brew`: "not recognized" lub "command not found"

Zamknij wszystkie terminale oraz VS Code, a następnie otwórz je
ponownie. Otwarte wcześniej okno może nie widzieć nowo zainstalowanego
programu. Jeśli problem nie zniknie, wróć do kroku instalacji w
przewodniku dla swojego systemu. Sprawdź wcześniejsze komunikaty w
terminalu i poszukaj błędu.

## Git Bash nie jest widoczny w terminalu VS Code (Windows)

Zobacz dedykowaną sekcję
[Nie widzę Git Bash w VS Code](windows.pl.md#nie-widzę-git-bash-w-vs-code)
w przewodniku dla Windows. Dowiesz się z niej, jak ponownie uruchomić
VS Code i ręcznie wskazać Git Bash.

## Mam problem z zalogowaniem się do GitHuba

- Sprawdź, czy podany adres e-mail lub nazwa użytkownika należą do
  Twojego konta GitHub i nie zawierają literówki.
- Jeśli uwierzytelnianie dwuetapowe prosi o kod, a nie masz przy sobie
  telefonu, wybierz na stronie logowania opcję "Use a recovery code"
  lub podobną. Użyj kodu otrzymanego podczas konfiguracji 2FA.
- Jeśli nie masz konta GitHub, utwórz je bezpłatnie na
  [github.com/join](https://github.com/join).

## GitHub mówi, że już mam fork tego repozytorium

Nie twórz kolejnego forka. Przejdź do
[`already-have-a-fork.pl.md`](already-have-a-fork.pl.md) i podążaj za
"Zrobiłem/am fork na GitHubie, ale nie wiem, czy go sklonowałem/am."

## Sklonowałem/am repozytorium prowadzącego zamiast własnego forka

Zobacz sekcję "Sklonowałem/am repozytorium prowadzącego zamiast
własnego forka" w przewodniku dla swojego systemu
([Windows](windows.pl.md#sklonowałemam-repozytorium-prowadzącego-zamiast-własnego-forka),
[macOS](macos.pl.md#sklonowałemam-repozytorium-prowadzącego-zamiast-własnego-forka),
[Linux](linux.pl.md#sklonowałemam-repozytorium-prowadzącego-zamiast-własnego-forka)).
Znajdziesz tam bezpieczne rozwiązanie, które nie usuwa plików.

## Repozytorium jest już gdzieś na moim dysku — nie chcę go klonować jeszcze raz

Przejdź do sekcji "Mam już lokalną kopię" w
[`already-have-a-fork.pl.md`](already-have-a-fork.pl.md). Pomoże Ci
znaleźć repozytorium i sprawdzić jego stan bez ponownego klonowania.

## Nie wiem, w jakim folderze jestem

Uruchom:
```bash
pwd
```
Polecenie wypisze bieżący katalog. Jeśli to nie jest oczekiwane
miejsce, wróć do katalogu domowego za pomocą `cd ~` i przejdź dalej od
tego punktu.

## VS Code otworzył niewłaściwy folder

Ponownie wybierz **File → Open Folder…** i wskaż katalog sklonowanego
repozytorium. Po otwarciu panel Explorer powinien zawierać katalogi
`labs`, `examples` i `scripts`. Jeśli ich nie widzisz, prawdopodobnie
otwarty jest katalog nadrzędny, na przykład `projects`, albo jeden z
podkatalogów repozytorium.

## "command not found" dla czegoś innego niż git/code/choco/brew

Sprawdź, czy korzystasz z terminala opisanego w przewodniku dla swojego
systemu: Git Bash na Windowsie albo domyślnego terminala na macOS i
Linuksie. Polecenia dostępne w jednej powłoce mogą nie działać w innej.
W razie wątpliwości zamknij terminal i otwórz go ponownie według
instrukcji.

## `labs/`, `examples/` albo `scripts/` brakuje w projekcie

Najczęściej oznacza to, że VS Code lub terminal jest otwarty w
niewłaściwym katalogu albo klonowanie nie zostało ukończone.
Uruchom:
```bash
pwd
ls
```
Jeśli `ls` nie pokazuje `labs`, `examples` i `scripts`, przejdź do
katalogu repozytorium za pomocą `cd` albo otwórz go ponownie przez
**File → Open Folder…**.

## Mój pierwszy `git push` nie działa

Zobacz [`github-auth.pl.md`](github-auth.pl.md). Przewodnik wyjaśnia
uwierzytelnianie przy pierwszym pushu oraz sytuację, w której nie
otwiera się przeglądarka.

## Git Bash mówi coś o końcach linii, albo skrypt `.sh` nie chce się uruchomić (Windows)

Komunikat podobny do `$'\r': command not found` zwykle oznacza
windowsowe końce linii. Pliki dostarczone z kursem mają już poprawny
format. Jeśli problem dotyczy niezmienionego pliku w `labs/`,
`examples/` lub `scripts/`, zgłoś go prowadzącemu. Jeśli plik powstał w
innym edytorze, otwórz go i zapisz w VS Code. Ten projekt konfiguruje
VS Code tak, aby używał właściwych końców linii.

## Jeśli wciąż utkniesz

Zgłoś problem prowadzącemu z tymi pięcioma informacjami:

1. Twój system operacyjny (Windows 11, macOS, albo która dystrybucja
   Linuksa).
2. Numer kroku lub nazwę sekcji.
3. Uruchomione polecenie.
4. Pełny komunikat błędu jako tekst lub zrzut ekranu.
5. Oczekiwany rezultat.

**Nie dołączaj do zgłoszenia hasła, personal access tokenu ani innych
poufnych danych.** Przed wysłaniem zrzutu ekranu przytnij lub zamaskuj
takie informacje.
