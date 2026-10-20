# Lab 03 — Dziedziczysz repozytorium

## Sytuacja

Masz już dostęp do repozytorium. Zanim cokolwiek zmienisz, sprawdź jego
stan i naucz się bezpiecznie zapisywać swoją pracę.

## Cele nauki

Po tym labie potrafisz:

- Wyjaśnić różnicę między katalogiem roboczym, obszarem stagingu a
  lokalnym repozytorium.
- Sprawdzić bieżący stan repozytorium za pomocą `git status`, `git log` i
  `git diff`.
- Dodać do stagingu i zacommitować zmianę z jasnym komunikatem.

## Zanim zaczniesz

- Lab 02 ukończony.
- Pracujesz we własnej lokalnej kopii repozytorium przygotowanej według
  [`START-HERE.pl.md`](../../START-HERE.pl.md). W Codespaces jest już
  dostępna.
- Bieżącym katalogiem jest katalog główny repozytorium.

## Twoje zadanie

1. Uruchom `git status` i `git log` w katalogu głównym repozytorium.
   Przeczytaj wynik, zanim zrobisz cokolwiek innego.
2. Utwórz nowy plik
   `labs/03-inherited-repository/notes/my-observations.txt` zawierający co
   najmniej dwa zdania. W jednym opisz wynik `git status`, a w drugim
   wynik `git log`.
3. Ponownie uruchom `git status`. W trzeciej linii tego samego pliku
   wyjaśnij własnymi słowami, dlaczego nowy plik ma taki status.
4. Dodaj do stagingu tylko ten plik poleceniem `git add`.
5. Uruchom `git diff --staged` i zaobserwuj, co pokazuje w porównaniu do
   zwykłego `git diff`.
6. Zacommituj zmianę ze stagingu z jasnym, angielskim komunikatem w czasie
   teraźniejszym, np. `docs: add lab 03 observations`. Jeśli to Twój
   pierwszy commit na tej maszynie, Git może zatrzymać Cię komunikatem
   "Please tell me who you are" zamiast utworzyć commit. Git zapisuje
   imię i adres e-mail autora przy każdym commicie. Uruchom te dwa
   polecenia raz (użyj adresu e-mail przypisanego do swojego
   konta GitHub), potem powtórz commit:
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com"
   ```
7. Uruchom `git log` jeszcze raz i potwierdź, że Twój commit jest
   pierwszy na liście.

## Kryteria akceptacji

- `labs/03-inherited-repository/notes/my-observations.txt` istnieje, jest
  zacommitowany i zawiera co najmniej trzy linie jak opisano wyżej.
- `git log` pokazuje Twój commit z jasnym, angielskim komunikatem.
- Potrafisz wyjaśnić, bez ponownego czytania dokumentacji Gita, co
  oznacza "staged".

## Weryfikacja

```bash
git log --oneline -1                      # your commit should be at HEAD
git status                                 # should be clean (nothing to commit)
test -f labs/03-inherited-repository/notes/my-observations.txt && echo "notes exist"
wc -l < labs/03-inherited-repository/notes/my-observations.txt  # expect >= 3
```

## Zastanów się

- `git diff` i `git diff --staged` pokazały różne rzeczy. Dlaczego Git w
  ogóle rozróżnia te dwa stany?
- Co stałoby się z nowym plikiem, gdyby polecenie `git commit` zostało
  uruchomione bez wcześniejszego `git add`?

## Jeśli utkniesz

- **Podpowiedź 1:** Potrzebujesz dokładnie pięciu poleceń Gita: `status`,
  `log`, `diff`, `add`, `commit`.
- **Podpowiedź 2:** `git diff` (bez argumentów) pokazuje zmiany poza
  stagingiem; `git diff --staged` pokazuje, co trafi do
  następnego commita.
- **Podpowiedź 3:** Commit potrzebuje komunikatu. Użyj `git commit -m
  "twoja wiadomość"` zamiast otwierać edytor, chyba że czujesz się z nim
  swobodnie.
- **Podpowiedź 4:** "Please tell me who you are" znaczy, że Git nie ma
  jeszcze skonfigurowanego `user.name` ani `user.email` na tej maszynie.
  W kroku 6 znajdziesz dwa polecenia `git config --global`, które
  uzupełniają tę konfigurację.

## Co dalej

Twój commit istnieje tylko na tej maszynie, w lokalnym repozytorium.
Nikt inny go jeszcze nie widzi. W następnym labie poznasz repozytorium
zdalne.

Przejdź do [Lab 04 — Lokalne to nie zdalne](../04-local-vs-remote/README.pl.md).
