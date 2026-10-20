# Lab 02 — Terminal jako narzędzie pracy

## Sytuacja

Wczoraj nauczyłeś/aś się poruszać po swoim środowisku. Dziś popracujesz
bez myszy: skopiujesz pliki, przeszukasz ich zawartość, połączysz
polecenia i zapanujesz nad procesem działającym aż do zatrzymania.

## Cele nauki

Po tym labie potrafisz:

- Kopiować, przenosić i usuwać pliki oraz katalogi z poziomu powłoki.
- Przeszukiwać zawartość plików i znajdować pliki po nazwie.
- Przekierowywać wyjście do pliku i przekazywać wyjście jednego polecenia
  do drugiego.
- Uruchomić długo działający proces, obserwować go i zatrzymać skrótem
  `Ctrl+C`.

## Zanim zaczniesz

- Lab 01 ukończony (potrafisz nawigować i tworzyć pliki z terminala).
- Możesz zacząć w katalogu domowym albo w lokalnej kopii repozytorium.

## Twoje zadanie

1. Wewnątrz `~/lab01-notes/` skopiuj `findings.txt` do `findings.bak.txt`.
2. Utwórz katalog `~/lab02-notes/` i przenieś do niego `findings.bak.txt`.
3. Wyszukaj w `findings.txt` linię zawierającą `whoami` za pomocą `grep`
   i przekieruj tę jedną pasującą linię do nowego pliku
   `~/lab02-notes/whoami-line.txt`.
4. Użyj `find`, żeby znaleźć każdy plik o nazwie `findings.txt` w dwóch
   utworzonych dotąd katalogach: `~/lab01-notes/` i `~/lab02-notes/`.
   Przekaż wynik do `wc -l`, żeby policzyć dopasowania:
   `find ~/lab01-notes ~/lab02-notes -name findings.txt | wc -l`.
   Wynikiem powinno być `1`. Oryginał nadal znajduje się w
   `lab01-notes`, a kopia z kroku 1 ma inną nazwę:
   `findings.bak.txt`.
5. Uruchom prosty, długo działający proces w katalogu domowym. Użyj
   pętli, która co sekundę dopisuje aktualny czas do pliku, aż ją
   zatrzymasz:
   `while true; do date >> ~/lab02-notes/ticker.txt; sleep 1; done`.
6. Gdy pętla działa, w **drugim terminalu** sprawdź liczbę linii dwa
   razy z kilkusekundową przerwą:
   `wc -l ~/lab02-notes/ticker.txt`. Za drugim razem wynik powinien być
   wyższy.
7. Zatrzymaj proces w pierwszym terminalu skrótem `Ctrl+C`. Potwierdź w
   drugim terminalu, że liczba linii przestała rosnąć: uruchom
   `wc -l ~/lab02-notes/ticker.txt` jeszcze dwa razy, z kilkusekundową
   przerwą, i oczekuj tej samej liczby oba razy.

## Kryteria akceptacji

- `~/lab02-notes/findings.bak.txt` oraz `~/lab02-notes/whoami-line.txt`
  istnieją, a drugi plik zawiera dokładnie jedną linię ze słowem
  `whoami`.
- Potrafisz wskazać dokładne polecenie, które zatrzymało proces, i
  wyjaśnić, jaki sygnał wysyła `Ctrl+C`.
- Potrafisz wyjaśnić różnicę między `>` a `>>`.

## Weryfikacja

```bash
test -f ~/lab02-notes/findings.bak.txt && echo "backup exists"
test -f ~/lab02-notes/whoami-line.txt && echo "grep output exists"
wc -l < ~/lab02-notes/whoami-line.txt   # expect exactly 1
find ~/lab01-notes ~/lab02-notes -name findings.txt | wc -l   # expect exactly 1
```

## Zastanów się

- Jaka jest praktyczna różnica między przekazaniem (`|`) jednego polecenia
  do drugiego a przekierowaniem (`>`) do pliku?
- Proces działał dalej po naciśnięciu Enter w pierwszym poleceniu. Dlaczego
  terminal nie wyświetlił od razu nowego promptu?
- Co zrobiłoby `>` (zamiast `>>`) z `findings.txt`, gdyby to była Twoja
  pomyłka w Lab 01?

## Jeśli utkniesz

- **Podpowiedź 1:** Potrzebujesz `cp`, `mv`, `rm` do operacji na plikach;
  `grep`, `find` i `wc` do wyszukiwania i zliczania; `>` i `>>` do
  przekierowań; `|` do przekazywania wyjścia.
- **Podpowiedź 2:** `grep "whoami" plik > wyjscie.txt` zapisuje pasujące
  linie z `plik` do `wyjscie.txt`, nadpisując go, jeśli istnieje.
- **Podpowiedź 3:** Żeby uruchomić polecenie i od razu odzyskać terminal,
  możesz wysłać je w tło przez `&`, ale w tym laboratorium użyj zamiast
  tego drugiej karty/panelu terminala, żeby obserwować oba naraz.

## Co dalej

Potrafisz już uruchamiać polecenia i sprawdzać ich wynik. Teraz
potrzebujesz sposobu na śledzenie zmian w projekcie i cofanie pomyłek.
Do tego służy system kontroli wersji.

Przejdź do [Lab 03 — Dziedziczysz repozytorium](../03-inherited-repository/README.pl.md).
