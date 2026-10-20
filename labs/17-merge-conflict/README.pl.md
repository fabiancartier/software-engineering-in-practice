# Lab 17 — Konflikt scalania

## Sytuacja

Obie funkcje są gotowe. Czas wprowadzić je do `main`, jedna po
drugiej.

## Cele nauki

Po tym labie potrafisz:

- Scalić gałąź bez konfliktów i rozpoznać poprawne scalenie.
- Przeczytać markery konfliktu Gita i zidentyfikować dokładnie, co
  każda strona zmieniła.
- Rozwiązać konflikt, łącząc obie zmiany, nie wybierając na oślep
  jednej strony.

## Zanim zaczniesz

- Najpierw ukończ lab 16. Gałęzie `feature/low-stock-warning` i
  `feature/expiry-warning` powinny istnieć, a testy na każdej z nich
  powinny przechodzić.

### Python

- Pracuj w `examples/team-inventory/python/` na gałęzi
  `main`.

### Go

- Pracuj w `examples/team-inventory/go/` na gałęzi `main`.

### Java

- Pracuj w `examples/team-inventory/java/` na gałęzi
  `main`.

## Twoje zadanie

1. Upewnij się, że jesteś na `main`, uruchamiając `git switch main`.
2. Scal pierwszą funkcję poleceniem `git merge feature/low-stock-warning`.
   Operacja powinna zakończyć się bez konfliktu. Przeczytaj komunikat,
   wypisuje Git (prawdopodobnie fast-forward, bo `main` się nie
   przesunął od Twojego rozgałęzienia).
3. Uruchom testy swojej ścieżki, aby potwierdzić, że `main` zawiera już
   ostrzeżenie o niskim stanie i nadal działa poprawnie.
4. Scal drugą funkcję poleceniem `git merge feature/expiry-warning`.
   Ta operacja **spowoduje** konflikt w **dwóch plikach**, źródłowym i
   testowym.
5. Otwórz swój plik źródłowy (zobacz dokładny kształt konfliktu Twojej
   ścieżki poniżej). Przeczytaj obie strony każdego bloku, zanim
   czegokolwiek dotkniesz.
6. Rozwiąż konflikt, zachowując **obie** zmiany. Usuń każdy marker
   konfliktu.
7. Otwórz swój plik testowy (zobacz dokładny kształt konfliktu Twojej
   ścieżki poniżej). Rozwiąż go, zachowując **obie** zmiany.
8. Uruchom testy swojej ścieżki. Test oryginalny oraz testy niskiego
   stanu i terminu ważności muszą przechodzić.
9. Dodaj oba rozwiązane pliki do indeksu i dokończ scalanie. Git
   przygotuje komunikat commitu scalającego; nie potrzebujesz `-m`.
10. Uruchom `git log --oneline --graph -5` i potwierdź, że obie funkcje
    są teraz częścią historii `main`.

### Szczegóły konfliktu w Pythonie

Dwa bloki w `inventory.py`: jeden, gdzie każda gałąź dodała swoją
własną nową funkcję (całe ciało funkcji się różni, bo dwa filtry są
napisane inaczej), jeden wewnątrz `summarize`, gdzie każda gałąź
dołączyła swoje własne dwie linie. Dwa bloki w
`tests/test_inventory.py`: linia `import` (każda gałąź importuje inną
nową nazwę) i nowa funkcja testowa dodana na każdej gałęzi. Każda ma
inną nazwę i wywołuje inną funkcję. To jeden blok
konfliktu, bo dwie funkcje testowe siedzą na bezpośrednio sąsiadujących
liniach).

### Szczegóły konfliktu w Go

To **nie jest** ten sam kształt co w Pythonie, i to jest oczekiwane,
nie Twoja pomyłka: Go nie ma linii `import`, na której mógłby być
konflikt (wywołanie innej funkcji w tym samym pakiecie nie potrzebuje
importu), więc `inventory_test.go` konfliktuje w dokładnie **jednym**
bloku obejmującym dwie sąsiadujące funkcje testowe, każdą nazwaną inaczej i
wywołująca inną funkcję. W `inventory.go` pierwszy blok konfliktu
obejmuje cztery linie z każdej strony: sygnaturę funkcji, linię `var
names []string`, linię pętli `for` i warunek `if`. Mimo że środkowe
dwie linie są identyczne na obu gałęziach, Git
wciąż umieszcza je wewnątrz markerów. Tylko pięcioliniowy ogon po tym
(`names = append(names, item.Name)`, dwa zamykające nawiasy klamrowe,
`return names`, i zamykający nawias klamrowy funkcji) jest wystarczająco
zgodny, żeby Git zmergował go automatycznie, poza markerami. Drugi
konflikt, wewnątrz `Summarize`, obejmuje oba wstawione bloki w całości,
bo żadna strona nie ma tam zgodnego ogona.

### Szczegóły konfliktu w Javie

Ten sam kształt co Go, z tego samego powodu: brak konfliktu na linii
importu (obie gałęzie wywołują statyczną metodę na tej samej klasie),
więc `InventoryTest.java` ma dokładnie **jeden** blok konfliktu,
dwie metody `@Test`, sąsiadujące (sama linia anotacji `@Test` siedzi
zaraz *przed* konfliktem i zgadza się na obu stronach, więc nie jest
częścią oznaczonego bloku). W `Inventory.java`, jak w Go, pierwszy
blok konfliktu obejmuje cztery linie z każdej strony: sygnaturę
metody, linię `List<String> names = new ArrayList<>();`, linię pętli
`for` i warunek `if`. Mimo że środkowe dwie linie są identyczne na obu
gałęziach, Git obejmuje je markerami. Tylko pięcioliniowy fragment po
tym (`names.add(item.name);`,
dwa zamykające nawiasy klamrowe, `return names;`, i zamykający nawias
klamrowy metody) scala się automatycznie poza markerami. Drugi
konflikt, wewnątrz `summarize`, obejmuje oba wstawione bloki w całości.

## Kryteria akceptacji

- Żadne markery konfliktu nie zostają nigdzie w Twoim pliku
  źródłowym albo pliku testowym.
- Obie nowe funkcje/metody są zdefiniowane i używane wewnątrz
  `summarize`/`Summarize`.
- Komenda testowa Twojej ścieżki przechodzi z każdym testem z obu
  gałęzi (łącznie 3 testy).
- Commit scalający `feature/expiry-warning` istnieje na `main`.

## Weryfikacja

### Python

```bash
cd examples/team-inventory/python
if grep -nE '^(<<<<<<<|=======|>>>>>>>)' inventory.py tests/test_inventory.py; then
    echo "Conflict markers remain."
    exit 1
else
    echo "No conflict markers remain."
fi
uv run pytest -v
git log --oneline -4
cd -
```

### Go

```bash
cd examples/team-inventory/go
if grep -nE '^(<<<<<<<|=======|>>>>>>>)' inventory.go inventory_test.go; then
    echo "Conflict markers remain."
    exit 1
else
    echo "No conflict markers remain."
fi
go test ./... -v
git log --oneline -4
cd -
```

### Java

```bash
cd examples/team-inventory/java
if grep -nE '^(<<<<<<<|=======|>>>>>>>)' src/main/java/Inventory.java src/test/java/InventoryTest.java; then
    echo "Conflict markers remain."
    exit 1
else
    echo "No conflict markers remain."
fi
./gradlew test
git log --oneline -4
cd -
```

Powinien pojawić się komunikat `No conflict markers remain.`, wszystkie
testy powinny przejść, a commit scalający powinien być widoczny w logu.
Zwróć uwagę na `exit 1` wewnątrz `if`. Bez niego kontrola wypisałaby
"Conflict markers remain.", ale zakończyłaby się sukcesem. Wypróbuj oba
warianty na pliku z markerami i na pliku bez nich.

## Zastanów się

- Żadna gałąź nie edytowała linii zmienionej przez drugą gałąź.
  obie tylko *dodały* nowe linie, w tym samym miejscu, w tych samych
  dwóch plikach. Czemu Git wciąż traktował oba pliki jako konflikty,
  zamiast po cichu zachować obie dodane rzeczy?
- Kolega z zespołu mówi "weź po prostu moje, usuń ich", bez czytania
  drugiej strony konfliktu. Jakie jest konkretne ryzyko robienia tego
  tutaj, w którymkolwiek z plików?

## Jeśli utkniesz

### Python

- **Podpowiedź 1.** `<<<<<<< HEAD` oznacza początek wersji z *bieżącej
  gałęzi*; `=======` dzieli obie strony; `>>>>>>>
  feature/expiry-warning` oznacza koniec wersji *przychodzącego*
  gałęzi.
- **Podpowiedź 2.** `inventory.py` ma dwa osobne bloki konfliktu; plik
  testowy też ma dwa. Jeden znajduje się na linii importu, a drugi
  obejmuje nazwę i ciało nowej funkcji testowej. Rozwiąż każdy blok; nie
  zatrzymuj się po pierwszym pliku.
- **Podpowiedź 3.** Po edycji w obu plikach nie może pozostać żadna
  linia `<<<<<<<`, `=======` ani `>>>>>>>`. Jeśli `grep` coś znajdzie,
  konflikt nie jest jeszcze rozwiązany.

### Go

- **Podpowiedź 1.** `<<<<<<< HEAD` oznacza początek wersji z *bieżącej
  gałęzi*; `=======` dzieli obie strony; `>>>>>>>
  feature/expiry-warning` oznacza koniec wersji *przychodzącego*
  gałęzi.
- **Podpowiedź 2.** `inventory.go` ma dwa bloki konfliktu, ale pierwszy
  jest krótszy niż cała funkcja. Kończy się przed liniami
  zamykającymi, które obie funkcje mają wspólne (`names = append(...)`
  przez finalne `}`), które nie są oznaczone wcale. Nie zakładaj, że
  musisz przepisać całą funkcję z markerów; przeczytaj, co faktycznie
  jest między nimi.
- **Podpowiedź 3.** Po edycji w obu plikach nie może pozostać żadna
  linia `<<<<<<<`, `=======` ani `>>>>>>>`. Jeśli `grep` coś znajdzie,
  konflikt nie jest jeszcze rozwiązany.

### Java

- **Podpowiedź 1.** `<<<<<<< HEAD` oznacza początek wersji z *bieżącej
  gałęzi*; `=======` dzieli obie strony; `>>>>>>>
  feature/expiry-warning` oznacza koniec wersji *przychodzącego*
  gałęzi.
- **Podpowiedź 2.** `Inventory.java` ma dwa bloki konfliktu, ale
  pierwszy jest krótszy niż cała metoda. Kończy się przed liniami
  zamykającymi, które obie metody mają wspólne (`names.add(...)` przez
  finalne `}`), które nie są oznaczone wcale. Nie zakładaj, że musisz
  przepisać całą metodę z markerów; przeczytaj, co faktycznie jest
  między nimi.
- **Podpowiedź 3.** Po edycji w obu plikach nie może pozostać żadna
  linia `<<<<<<<`, `=======` ani `>>>>>>>`. Jeśli `grep` coś znajdzie,
  konflikt nie jest jeszcze rozwiązany.

Przed przejściem dalej utwórz commit ze wszystkim z tego laba i
wypchnij go do repozytorium
(`git add -A && git commit -m "..."; git push`). Nic później jeszcze
nie wymaga czystego drzewa roboczego, ale Akt IV już tak. Warto od razu
wyrobić sobie ten nawyk.

## Co dalej

Konflikt został rozwiązany lokalnie, a scalenie trafiło bezpośrednio na
`main`. W prawdziwym zespole taka zmiana zwykle przechodzi najpierw
przegląd kodu. W następnym labie wykonasz ten proces.

Przejdź do [Lab 18 — Pull request i przegląd kodu](../18-pull-requests-and-review/README.pl.md).
