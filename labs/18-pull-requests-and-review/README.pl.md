# Lab 18 — Pull request i przegląd kodu

## Sytuacja

Dotychczas każda zmiana trafiała na `main` po samodzielnym scaleniu.
Przed scaleniem kod powinien jednak zobaczyć ktoś jeszcze. Może to być
dziś osoba z grupy albo Ty, gdy wrócisz do zmiany później.

## Cele nauki

Po tym labie potrafisz:

- Otworzyć pull request z opisem wyjaśniającym, dlaczego zmiana powstała,
  nie tylko co się zmieniło.
- Przejrzeć różnice według konkretnej listy kontrolnej, zamiast polegać
  na ogólnym wrażeniu.
- Zakończyć przegląd przed scaleniem konkretnym wynikiem: rozwiązanym
  komentarzem wymagającym działania albo uzasadnionym zatwierdzeniem.

## Zanim zaczniesz

- Najpierw ukończ lab 17. `main` powinien zawierać ostrzeżenia o niskim
  stanie i terminie ważności w wybranej ścieżce.
- Twoja praca `examples/team-inventory/` mieszka w **Twoim własnym**
  repozytorium lub forku na GitHubie. Pull request z tego laba tworzysz
  właśnie tam, a nie we wspólnym repozytorium kursu.
- Jeśli prowadzący przydzielił Ci osobę do pracy w parze, w kroku 4
  wymienicie się pull requestami.

### Python

- Pracuj w `examples/team-inventory/python/`.

### Go

- Pracuj w `examples/team-inventory/go/`.

### Java

- Pracuj w `examples/team-inventory/java/`.

## Twoje zadanie

1. Utwórz gałąź `feature/reorder-report` z `main`.

### Python

2. Dodaj funkcję `reorder_report(inventory: list[dict], threshold: int
   = 5) -> str`, która ponownie używa `low_stock_items` i zwraca
   napis w rodzaju `"Reorder needed: Tomatoes, Milk"` (albo
   `"Nothing to reorder."`, jeśli lista jest pusta). Wstaw ją
   bezpośrednio powyżej `summarize`. Dodaj test i utwórz commit.

### Go

2. Dodaj funkcję `func ReorderReport(inventory []Item, threshold int)
   string`, która korzysta z `LowStockItems` i zwraca napis w rodzaju
   `"Reorder needed: Tomatoes, Milk"` (albo `"Nothing to
   reorder."`, jeśli lista jest pusta). Wstaw ją bezpośrednio powyżej
   `Summarize`. Dodaj test i utwórz commit.

### Java

2. Dodaj metodę `public static String reorderReport(List<Item>
   inventory, int threshold)`, która ponownie używa `lowStockItems` i
   zwraca napis w rodzaju `"Reorder needed: Tomatoes, Milk"`
   (albo `"Nothing to reorder."`, jeśli lista jest pusta). Wstaw ją
   bezpośrednio powyżej `summarize`. Dodaj test i utwórz commit.

## Wszystkie ścieżki

3. Wypchnij gałąź i otwórz pull request. Najpierw sprawdź, dokąd
   faktycznie trafi. **Nie zgaduj.**
   - **Interfejs WWW GitHuba** jest najpewniejszą drogą przy pierwszym
     pull requeście. Kliknij **Compare & pull request** na wypchniętej gałęzi albo
     przejdź do zakładki **Pull requests** swojego forka i kliknij
     **New pull request**. Przed kliknięciem finalnego przycisku
     **Create pull request**, sprawdź **base repository** i **base
     branch** pokazane na górze strony. Muszą wskazywać *Twój własny*
     forkiem i Twoim własnym `main`, nie repozytorium kursu, z którego
     pierwotnie utworzono fork. GitHub czasem wybiera je jako bazę, a
     tutaj byłby to błędny cel.
   - **`gh pr create`** jest opcjonalne i dotyczy tylko osób, które już
     mają GitHub CLI. Kurs nie wymaga jego instalowania. Polecenie może
     domyślnie kierować PR do repozytorium źródłowego. Uruchom raz
     `gh repo set-default <twój-fork>` i przed potwierdzeniem ponownie
     sprawdź repozytorium bazowe.

   Nigdy nie kieruj tego (albo jakiegokolwiek przyszłego) PR do
   wspólnego repozytorium kursu. Celem zawsze jest `main` Twojego
   forka.

   W opisie podaj, co się zmieniło, dlaczego oraz jak zostało
   sprawdzone, łącznie z uruchomionymi poleceniami.
4. Przejrzyj zmianę według poniższej listy.
   - **W parze.** Wymień się pull requestami z osobą przydzieloną przez
     prowadzącego i przejrzyjcie nawzajem swoje zmiany.
   - **Samodzielnie.** Przeczytaj własne różnice tak, jakby ich autor
     był Ci obcy. Użyj tej samej listy kontrolnej.

   Lista kontrolna:
   - Czy opis wyjaśnia *czemu*, nie tylko *co*?
   - Czy test faktycznie ćwiczy nowe zachowanie, nie tylko wywołuje
     funkcję raz?
   - Czy jest tu logika zduplikowana z funkcji niskiego stanu, która
     powinna być ponownie użyta, nie przepisana?
   - Czy różnice są zrozumiałe bez dodatkowych pytań do autora?
5. Dalsze kroki zależą od wyniku przeglądu.
   - **Znaleziono problem wymagający działania.** Powinno to być coś,
     co rzeczywiście warto zmienić lub wyjaśnić, a nie opisanie różnic
     innymi słowami. Zostaw co najmniej jeden merytoryczny komentarz
     (na GitHubie,
     jeśli w parze; w
     `labs/18-pull-requests-and-review/my-review-notes.md`, jeśli
     samodzielnie), a następnie odnieś się do niego. Popraw kod albo
     wyjaśnij, dlaczego zmiana nie jest potrzebna, i sprawdź rezultat.
   - **Nie znaleziono problemu wymagającego działania.** Zatwierdź PR
     na GitHubie, jeśli pracujesz w parze. Przy pracy samodzielnej
     napisz w `my-review-notes.md` jedno konkretne zdanie potwierdzające,
     że każdy punkt listy został sprawdzony. Nie wymyślaj komentarza
     tylko po to, aby spełnić wymaganie.
6. Po zakończeniu przeglądu scal PR przyciskiem GitHuba, a nie lokalnym
   `git merge`.
7. Pobierz scaloną zmianę do lokalnej gałęzi `main`.

## Kryteria akceptacji

- Pull request kierował do `main` **Twojego własnego forka**, nigdy do
  repozytorium kursu. Opis zawierał zakres i powód zmiany oraz sposób
  jej sprawdzenia.
- Przegląd zakończył się jednym z dwóch poprawnych wyników. Merytoryczny
  komentarz został rozwiązany albo lista kontrolna nie wykazała nic do
  poprawy, co potwierdza zatwierdzenie na GitHubie przy pracy w parze
  lub konkretny zapis w `my-review-notes.md` przy pracy samodzielnej.
  GitHub nie pozwala zatwierdzić własnego pull requesta, dlatego notatka
  pełni tę samą rolę. Nie twórz sztucznego komentarza tylko po to, aby
  spełnić wymaganie.
- Po pobraniu, lokalny `main` zawiera nową funkcję/metodę i jej test, a
  komenda testowa Twojej ścieżki przechodzi.

## Weryfikacja

### Python

```bash
cd examples/team-inventory/python
git log --oneline -3
uv run pytest -v
cd -
```

### Go

```bash
cd examples/team-inventory/go
git log --oneline -3
go test ./... -v
cd -
```

### Java

```bash
cd examples/team-inventory/java
git log --oneline -3
./gradlew test
cd -
```

W historii powinien być widoczny commit scalający lub squash commit dla
`feature/reorder-report`, zależnie od ustawień repozytorium. Wszystkie
testy powinny przejść.

## Zastanów się

- Czym różni się sprawdzenie „czy to działa” od sprawdzenia „czy kolejna
  osoba zrozumie ten kod”? Do którego podejścia skłania lista kontrolna?
- Jeśli przegląd był samodzielny, co udało się zauważyć ponad to, co
  pokazały same testy?

## Jeśli utkniesz

- **Podpowiedź 1.** W interfejsie WWW repozytorium i gałąź bazowa są
  pokazane w dwóch listach na górze strony "Open a pull request".
  Sprawdź je przed przeczytaniem pozostałej części strony.
- **Podpowiedź 2.** „Ponownie użyj funkcji niskiego stanu” oznacza
  wywołanie jej z nowej funkcji lub metody, a nie skopiowanie jej
  logiki filtrowania do drugiego miejsca.
- **Podpowiedź 3.** Przy pracy samodzielnej napisz komentarz lub notatkę
  zatwierdzenia tak, jakby za sześć miesięcy nie było już żadnego
  kontekstu. Wtedy od razu widać, że ogólne notatki są bezużyteczne,
  niezależnie od wyniku przeglądu.

Przed przejściem dalej utwórz commit ze wszystkim z tego laba i
wypchnij go do repozytorium
(`git add -A && git commit -m "..."; git push`). Nic później jeszcze
nie wymaga czystego drzewa roboczego, ale Akt IV już tak. Warto od razu
wyrobić sobie ten nawyk.

## Co dalej

Nawet przejrzany i scalony kod jest tak dobry, jak zakres wykonanych
kontroli. W następnym labie repozytorium zacznie sprawdzać się samo.

Przejdź do [Lab 19 — Repozytorium powinno sprawdzać samo siebie](../19-repository-checks-itself/README.pl.md).
