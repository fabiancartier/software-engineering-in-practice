# Lab 01 — Witaj na swoim stanowisku pracy

## Sytuacja

Właśnie dołączasz do projektu i dostajesz świeżo skonfigurowany laptop.
Zanim zmienisz kod, musisz nauczyć się poruszać po swoim środowisku.

## Cele nauki

Po tym labie potrafisz:

- Ustalić swoją bieżącą lokalizację w systemie plików i poruszać się między
  katalogami.
- Wyświetlić pliki, w tym ukryte, i odczytać szczegółową listę ich
  właściwości.
- Tworzyć i sprawdzać pliki oraz katalogi z poziomu terminala.
- Wyjaśnić własnymi słowami, czym są `$HOME`, `$PATH` i `$SHELL`.

## Zanim zaczniesz

- Masz własną kopię tego repozytorium otwartą w VS Code, z działającym
  terminalem. Jeśli jeszcze jej nie masz, zacznij od
  [`START-HERE.pl.md`](../../START-HERE.pl.md).
- Nie musisz mieć za sobą żadnego wcześniejszego laboratorium.

## Twoje zadanie

Pracując wyłącznie w terminalu:

1. Sprawdź swój bieżący katalog, potem przejdź do katalogu domowego i
   potwierdź, że tam jesteś.
2. Wyświetl zawartość katalogu domowego, w tym pliki ukryte.
3. Utwórz katalog `lab01-notes` wewnątrz katalogu domowego.
4. W jego wnętrzu utwórz plik `findings.txt`.
5. Uruchom `echo "$SHELL"`. Wynik pokazuje skonfigurowaną powłokę
   logowania.
6. Zapisz do `findings.txt` wynik tych pięciu poleceń, po jednym w linii,
   opisanych etykietą: `whoami`, `uname -a`, `echo "$HOME"`,
   `echo "$PATH"`, `echo "$SHELL"`. Nie znasz jeszcze edytora tekstu,
   więc skorzystaj z operatora `>>`. Dopisuje on wynik polecenia do
   pliku bez jego otwierania:
   `echo "whoami: $(whoami)" >> ~/lab01-notes/findings.txt` dodaje
   jedną opisaną linię. Powtórz to dla każdego z pięciu poleceń.
   Operatory `>>`, `>` i `|` poznasz dokładniej w Labie 02.
7. Użyj `which`, żeby sprawdzić, gdzie na dysku znajdują się
   programy `git` i `code`.
8. Otwórz `findings.txt` poleceniem `less` i potwierdź jego zawartość.

## Kryteria akceptacji

- `~/lab01-notes/findings.txt` istnieje i zawiera pięć opisanych linii z
  wynikiem uzyskanym na Twoim komputerze.
- Potrafisz, bez ponownego sprawdzania, podać ścieżkę swojego katalogu
  domowego i nazwę swojego skonfigurowanego shella logowania.
- Potrafisz w jednym lub dwóch zdaniach wyjaśnić, do czego służy `$PATH`.

## Weryfikacja

```bash
test -f ~/lab01-notes/findings.txt && echo "file exists"
wc -l < ~/lab01-notes/findings.txt   # expect at least 5
which git
which code
```

Jeśli oba polecenia `which` wyświetlają ścieżkę zamiast błędu, powłoka
potrafi znaleźć te narzędzia.

## Zastanów się

- Co by się stało, gdyby `$PATH` nie zawierał katalogu, w którym znajduje
  się `git`?
- Dwa różne konta na tej samej maszynie mogą mieć różne `$HOME`. Dlaczego
  ma to znaczenie dla skryptu, który zakłada stałą lokalizację pliku?

## Jeśli utkniesz

- **Podpowiedź 1:** Każde zadanie wykonasz jednym krótkim poleceniem. Z
  nowych opcji potrzebujesz tylko `-la` do wyświetlenia ukrytych plików
  i `-a` dla `uname`.
- **Podpowiedź 2:** Potrzebujesz `pwd`, `cd`, `ls -la`, `mkdir`,
  `touch`, `cat`, `whoami`, `uname -a`, `echo`, `which`, `less`.
- **Podpowiedź 3:** Jeśli nie masz pewności, czy operator `>>`
  zadziałał, uruchom potem `cat ~/lab01-notes/findings.txt` i potwierdź,
  że wszystkie pięć linii tam jest. Każde dopisanie powinno dodać jedną
  nową linię.

## Co dalej

Potrafisz już poruszać się po swoim środowisku i je sprawdzać. W
następnym labie zaczniesz łączyć polecenia i zarządzać uruchomionymi
procesami.

Przejdź do [Lab 02 — Terminal jako narzędzie pracy](../02-terminal/README.pl.md).
