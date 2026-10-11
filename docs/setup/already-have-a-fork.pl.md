# Masz już fork albo lokalną kopię?

[In English →](already-have-a-fork.md) · [← Wróć do Zacznij tutaj](../../START-HERE.pl.md)

Ten przewodnik jest dla osób, które mają już fork lub lokalną kopię
repozytorium, ale nie wiedzą, od którego miejsca kontynuować. Poniższe
kroki niczego nie usuwają ani nie nadpisują. Jeśli dalsza praca
wymagałaby takiej operacji, zatrzymaj się i poproś o pomoc.

## "Zrobiłem/am fork na GitHubie, ale nie wiem, czy go sklonowałem/am"

1. Otwórz [github.com](https://github.com) w przeglądarce, kliknij swój
   avatar (prawy górny róg), kliknij **Your repositories**.
2. Poszukaj `software-engineering-in-practice` na liście. Jeśli je
   widzisz, fork już istnieje. Nie twórz kolejnego.
3. Sprawdź, czy repozytorium znajduje się też na Twoim komputerze.
   Otwórz terminal i uruchom:
   ```bash
   find ~ -maxdepth 4 -iname "software-engineering-in-practice" -type d 2>/dev/null
   ```
   Jeśli pojawi się ścieżka, przejdź do sekcji "Mam już lokalną kopię".
   Jeśli polecenie niczego nie wypisze, przejdź do kroku "Sklonuj swój
   fork" w przewodniku dla swojego systemu:
   ([Windows](windows.pl.md#63-sklonuj-repozytorium-na-swój-komputer),
   [macOS](macos.pl.md#63--sklonuj-je-na-swój-komputer),
   [Linux](linux.pl.md#63--sklonuj-je-na-swój-komputer)).

## "Mam już lokalną kopię"

1. Otwórz terminal (albo ten wewnątrz VS Code) i przejdź do folderu,
   który wypisała komenda `find` powyżej, na przykład:
   ```bash
   cd ~/projects/software-engineering-in-practice
   ```
2. Sprawdź, czy repozytorium wskazuje na Twój fork:
   ```bash
   git remote -v
   ```
   **Jeśli widzisz swoją nazwę użytkownika z GitHuba**, przejdź do
   sekcji "Otwieranie jej ponownie".

   **Jeśli widzisz `michalmaj` zamiast swojej nazwy użytkownika**, masz
   klon repozytorium prowadzącego. Nie usuwaj go. Zmień nazwę katalogu,
   a potem sklonuj swój fork:
   ```bash
   cd ..
   mv software-engineering-in-practice software-engineering-in-practice.instructor-copy
   ```
   Następnie wykonaj krok "Sklonuj swój fork" z przewodnika dla
   swojego systemu.

3. Sprawdź stan lokalnych plików:
   ```bash
   git status
   ```
   - `nothing to commit, working tree clean` oznacza, że możesz
     bezpiecznie kontynuować.
   - Każdy inny wynik może oznaczać zmodyfikowane lub nowe pliki.
     **Nie usuwaj tych zmian.** Jeśli nie wiesz, skąd się wzięły,
     pozostaw je bez zmian i zapytaj prowadzącego. Nie uruchamiaj
     poleceń, które odrzucają pracę (zobacz
     [Czego nigdy tutaj nie robić](#czego-nigdy-tutaj-nie-robić)
     poniżej) tylko po to, żeby uzyskać czyste drzewo robocze.

## Otwieranie jej ponownie

1. Otwórz VS Code.
2. **File → Open Folder…**, przejdź do folderu z kroku powyżej.
3. Otwórz terminal wewnątrz VS Code (**Terminal → New Terminal**) i
   potwierdź:
   ```bash
   pwd
   git status
   ```
   Jesteś we właściwym miejscu, jeśli wynik `pwd` kończy się na
   `/software-engineering-in-practice`, a `git status` działa bez błędu.

## "Mój fork wygląda nieaktualny w porównaniu z repozytorium kursu"

Fork może pozostać w tyle, gdy prowadzący zaktualizuje repozytorium
kursu. Nie musisz go synchronizować przed Labem 01. Zrób to dopiero
wtedy, gdy poprosi o to konkretny lab. Użyj funkcji dostępnej na
GitHubie:

1. Na stronie swojego forka na GitHubie poszukaj przycisku **Sync
   fork** (GitHub pokazuje go automatycznie, gdy Twój fork jest w
   tyle).
2. Kliknij go, potem **Update branch**.
3. W terminalu zaktualizuj lokalną kopię:
   ```bash
   git pull
   ```

Jeśli `git pull` zgłosi konflikt, zatrzymaj się i zapytaj prowadzącego.
Nie próbuj go usuwać poleceniami z następnej sekcji.

## Czego nigdy tutaj nie robić

Nie używaj poniższych poleceń do naprawiania konfiguracji. Każde z nich
może bezpowrotnie usunąć pracę, której nie ma jeszcze na GitHubie:

- `git reset --hard`
- `git clean -fd` (albo `-fdx`)
- `rm -rf` na folderze Twojego repozytorium
- `git push --force` ani `git push -f`

Jeśli wydaje Ci się, że potrzebujesz jednego z tych poleceń, zatrzymaj
się i opisz problem prowadzącemu. Wskazówki znajdziesz w
[`troubleshooting.pl.md`](troubleshooting.pl.md).

## Nadal nie wiesz, od czego zacząć?

Zajrzyj do [`troubleshooting.pl.md`](troubleshooting.pl.md) albo wróć do
[Zacznij tutaj](../../START-HERE.pl.md) i ponownie przejdź przez
przewodnik dla swojego systemu. Nie zgaduj, jeśli rozwiązanie mogłoby
narazić Twoją pracę.
