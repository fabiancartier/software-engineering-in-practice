# Lab 04 — Lokalne to nie zdalne

## Sytuacja

Twój commit z Labu 03 jest zapisany w lokalnym repozytorium. Ktoś z
zespołu pyta: „Te zmiany są już na GitHubie?”. Nie wiesz jeszcze, jak
to sprawdzić.

## Cele nauki

Po tym labie potrafisz:

- Wyjaśnić różnicę między katalogiem roboczym, lokalnym repozytorium a
  repozytorium zdalnym.
- Sprawdzić, jakie repozytoria zdalne są skonfigurowane w lokalnym
  repozytorium.
- Opublikować lokalny commit na zdalnym repozytorium poleceniem `git push`
  i pobrać zmiany innych osób poleceniem `git pull`.
- Wyjaśnić, że Git (narzędzie) i GitHub (usługa hostująca repozytoria Git)
  to nie to samo.

## Zanim zaczniesz

- Lab 03 ukończony. Masz co najmniej jeden lokalny commit.
- Twoje repozytorium ma skonfigurowane repozytorium zdalne. Klonowanie
  forka według [`START-HERE.pl.md`](../../START-HERE.pl.md) lub użycie
  Codespaces ustawia je automatycznie.
- Bieżącym katalogiem jest katalog główny repozytorium.
- Jeśli `git remote -v` pokazuje adres URL, który *nie* jest Twoim
  własnym forkiem (wskazuje na oryginalne repozytorium kursu), zatrzymaj
  się i przeczytaj
  [`docs/setup/already-have-a-fork.pl.md`](../../docs/setup/already-have-a-fork.pl.md),
  zanim przejdziesz dalej. Próba wysłania zmian do cudzego repozytorium
  zakończy się błędem uprawnień.
- W tym labie po raz pierwszy wykonasz push do GitHuba. Jeśli Git poprosi
  o uwierzytelnienie i nie wiesz, co zrobić,
  zobacz [`docs/setup/github-auth.pl.md`](../../docs/setup/github-auth.pl.md).

## Twoje zadanie

1. Uruchom `git remote -v` i zanotuj adresy URL przypisane do `origin`.
2. Uzupełnij
   `labs/04-local-vs-remote/notes/my-observations.txt` o jedno zdanie
   wyjaśniające własnymi słowami, do czego odnosi się `origin`.
3. Dodaj i zacommituj ten plik (`docs: add lab 04 observations`).
4. Uruchom `git push`. Jeśli się powiedzie, Twoje commity (z tego
   laboratorium i z Labu 03, jeśli nie były jeszcze wysłane) znajdą się
   również w repozytorium zdalnym.
5. Uruchom lokalnie `git log`, a potem porównaj go z historią commitów
   dla tej samej gałęzi w interfejsie repozytorium zdalnego, na przykład
   w widoku "Commits" na GitHubie.
6. Uruchom `git pull`. Nawet jeśli nie ma nowych zmian, polecenie powinno
   zakończyć się bez błędu. W dalszej części kursu będziesz nim pobierać
   pracę innych osób.

## Kryteria akceptacji

- Wynik `git remote -v` jest zapisany (własnymi słowami) w
  `my-observations.txt`.
- Twoje commity z Labów 03 i 04 są widoczne zarówno w lokalnym
  `git log`, jak i w historii zdalnej gałęzi.
- Potrafisz na głos podać jedno zdanie odróżniające "repozytorium lokalne"
  od "repozytorium zdalnego" oraz jedno zdanie odróżniające Git od
  GitHuba.

## Weryfikacja

```bash
git remote -v
git log --oneline -3
git status   # expect "up to date" and "nothing to commit"
```

Następnie otwórz repozytorium na GitHubie (albo swoim hoście Gita) i
potwierdź, że Twój ostatni commit też się tam pojawia.

## Zastanów się

- Jeśli nigdy nie uruchomisz `git push`, czy Twoja praca istnieje
  gdziekolwiek poza Twoją własną maszyną?
- `git pull` łączy dwie operacje: fetch i merge. Dlaczego warto o tym
  pamiętać podczas pracy zespołowej?

## Jeśli utkniesz

- **Podpowiedź 1:** Potrzebujesz trzech nowych poleceń poza Lab 03: `git
  remote -v`, `git push`, `git pull`.
- **Podpowiedź 2:** Jeśli `git push` zostanie odrzucony, zwykle oznacza to,
  że repozytorium zdalne ma commity, których nie masz lokalnie. Najpierw
  wykonaj `git pull`, a potem ponów push.
- **Podpowiedź 3:** `origin` jest lokalnym aliasem adresu zdalnego
  repozytorium. To umowna nazwa domyślna, a nie specjalne słowo Gita.

## Co dalej

Potrafisz już wskazać, gdzie znajduje się Twój kod. Do tej pory każdy
projekt był jednak na tyle mały, że dało się go uruchomić z pamięci.
Następny lab pokaże, co się dzieje, gdy projekt działa tylko na jednym
komputerze.

Przejdź do [Lab 05 — "Działa na moim komputerze"](../05-works-on-my-machine/README.pl.md).
