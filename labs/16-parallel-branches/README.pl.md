# Lab 16 — Gałęzie istnieją, bo praca toczy się równolegle

## Sytuacja

Ty i druga osoba z zespołu musicie dziś dodać ostrzeżenia do skryptu
obsługującego magazyn kuchenny, bez czekania na siebie. Gałęzie
pozwalają zacząć z tego samego miejsca i pracować równolegle, a
niedokończone zmiany pozostają rozdzielone.

To pierwszy lab w Akcie IV. Podobnie jak w Aktach II i III, Python, Go
i Java tworzą kompletne ścieżki. Czytaj tylko instrukcje dla wybranego
języka.

## Cele nauki

Po tym labie potrafisz:

- Utworzyć nową gałąź w konkretnym punkcie historii i przełączyć się na
  nią.
- Wyświetlić istniejące gałęzie i wyjaśnić, co zawiera każda z nich.
- Przeczytać wynik `git log --all --graph` i zidentyfikować
  rozbieżną historię.

## Zanim zaczniesz

- Najpierw ukończ laby 06-15 w wybranej ścieżce.
- Polecenia Gita w tym labie (`git switch`, `git branch`, `git merge`,
  `git commit`) operują na historii **całego repozytorium kursu**, nie
  na osobnym repozytorium w katalogu wybranego języka. Każda utworzona
  gałąź należy do całego repozytorium, nawet jeśli zmieniasz tylko
  pliki jednej ścieżki.

### Python

- Pracuj w `examples/team-inventory/python/`.
- Sprawdź wersję startową poleceniami `uv run pytest -v` i `uv run python
  inventory.py`.

### Go

- Pracuj w `examples/team-inventory/go/`.
- Sprawdź wersję startową poleceniami `go test ./...` i `go run .`.

### Java

- Pracuj w `examples/team-inventory/java/`.
- Sprawdź wersję startową poleceniami `./gradlew test` i `./gradlew run
  --console=plain`.

## Twoje zadanie

Wykonasz pracę obu osób z zespołu, po jednej gałęzi naraz. Punkt
startowy, nazwy gałęzi i kolejność są takie same w każdej ścieżce.
Różni się tylko zawartość plików.

1. Sprawdź, czy zaczynasz od czystej gałęzi `main`. Uruchom
   `git switch main`, a następnie `git status --short`. Jeśli drugie
   polecenie coś wypisze, najpierw utwórz commit albo odłóż zmiany w
   stashu. Obie gałęzie muszą zacząć w tym samym punkcie.

**Osoba A, ostrzeżenie o niskim stanie**

2. Na `main` utwórz nową gałąź i przełącz się na nią.
   `git switch -c feature/low-stock-warning`.

### Python

3. W `inventory.py` dodaj
   `low_stock_items(inventory: list[dict], threshold: int = 5) -> list[str]`,
   zwracającą nazwy pozycji, których `quantity` jest poniżej
   `threshold`. Wstaw ją **bezpośrednio powyżej `summarize`**.
4. W `summarize`, zaraz po pętli `for` i przed linią `return`, dodaj:
   ```python
       low_stock = low_stock_items(inventory)
       if low_stock:
           lines.append(f"Low stock: {', '.join(low_stock)}")
   ```
5. W `tests/test_inventory.py` zmień import w pierwszej linii na
   `from inventory import low_stock_items, summarize`, potem dodaj
   test bezpośrednio po `test_summarize_lists_each_item_with_quantity`:
   ```python
   def test_low_stock_items_lists_items_below_threshold():
       inventory = [{"name": "Milk", "quantity": 2, "expires_in_days": 1}]

       result = low_stock_items(inventory)

       assert result == ["Milk"]
   ```
6. Uruchom `uv run pytest -v`, a potem utwórz na tej gałęzi commit ze
   wszystkimi zmianami.

### Go

3. W `inventory.go` dodaj
   `func LowStockItems(inventory []Item, threshold int) []string`,
   zwracającą nazwy pozycji, których `Quantity` jest poniżej
   `threshold`. W Go nie ma domyślnych argumentów, więc `threshold`
   zawsze podajesz jawnie. Z `Summarize` przekażesz `5`.
   Wstaw funkcję **bezpośrednio powyżej `Summarize`**.
4. W `Summarize`, zaraz po pętli `for` i przed linią `return`, dodaj:
   ```go
   	lowStock := LowStockItems(inventory, 5)
   	if len(lowStock) > 0 {
   		lines = append(lines, fmt.Sprintf("Low stock: %s", strings.Join(lowStock, ", ")))
   	}
   ```
5. W `inventory_test.go` dodaj test bezpośrednio po
   `TestSummarizeListsEachItemWithQuantity`:
   ```go
   func TestLowStockItemsListsItemsBelowThreshold(t *testing.T) {
   	inventory := []Item{{Name: "Milk", Quantity: 2, ExpiresInDays: 1}}

   	result := LowStockItems(inventory, 5)

   	if len(result) != 1 || result[0] != "Milk" {
   		t.Errorf("got %v, want [Milk]", result)
   	}
   }
   ```
6. Uruchom `gofmt -l .` (oczekiwane: brak wyniku) i `go test ./... -v`,
   a potem utwórz na tej gałęzi commit ze wszystkimi zmianami.

### Java

3. W `Inventory.java` dodaj
   `public static List<String> lowStockItems(List<Item> inventory, int threshold)`,
   zwracającą nazwy pozycji, których `quantity` jest poniżej
   `threshold`. Wstaw ją **bezpośrednio powyżej `summarize`**.
4. W `summarize`, zaraz po pętli `for` i przed linią `return`, dodaj:
   ```java
           List<String> lowStock = lowStockItems(inventory, 5);
           if (!lowStock.isEmpty()) {
               lines.add("Low stock: " + String.join(", ", lowStock));
           }
   ```
5. W `InventoryTest.java` dodaj test bezpośrednio po
   `summarizeListsEachItemWithQuantity`, i dodaj
   `import static org.junit.jupiter.api.Assertions.assertEquals;`
   obok istniejącego importu `assertTrue`:
   ```java
       @Test
       void lowStockItemsListsItemsBelowThreshold() {
           List<Inventory.Item> inventory = List.of(new Inventory.Item("Milk", 2, 1));

           List<String> result = Inventory.lowStockItems(inventory, 5);

           assertEquals(List.of("Milk"), result);
       }
   ```
6. Uruchom `./gradlew test`, a potem utwórz na tej gałęzi commit ze
   wszystkimi zmianami.

**Osoba B, ostrzeżenie o terminie ważności**

7. Wróć na `main`. **Nie scalaj jeszcze gałęzi
   `feature/low-stock-warning`.**
8. Na `main` utwórz nową gałąź i przełącz się na nią.
   `git switch -c feature/expiry-warning`.

### Python

9. W `inventory.py` dodaj
   `expiring_items(inventory: list[dict], days: int = 3) -> list[str]`,
   zwracającą nazwy pozycji, których `expires_in_days` jest `<=`
   `days`. Wstaw ją **bezpośrednio powyżej `summarize`**, w tym samym
   miejscu co w kroku 3, bo zaczynasz z tego samego `main` co osoba A.
10. W `summarize`, w **tym samym miejscu** co krok 4 (zaraz po pętli
    `for`, przed `return`), dodaj:
    ```python
        expiring = expiring_items(inventory)
        if expiring:
            lines.append(f"Expiring soon: {', '.join(expiring)}")
    ```
11. W `tests/test_inventory.py` zmień import w pierwszej linii na
    `from inventory import expiring_items, summarize`, potem dodaj
    test bezpośrednio po
    `test_summarize_lists_each_item_with_quantity`, w tym samym miejscu
    co krok 5:
    ```python
    def test_expiring_items_lists_items_within_days():
        inventory = [{"name": "Milk", "quantity": 2, "expires_in_days": 1}]

        result = expiring_items(inventory)

        assert result == ["Milk"]
    ```
12. Uruchom `uv run pytest -v`, a potem utwórz na tej gałęzi commit ze
    wszystkimi zmianami.

### Go

9. W `inventory.go` dodaj
   `func ExpiringItems(inventory []Item, days int) []string`, zwracającą
   nazwy pozycji, których `ExpiresInDays` jest `<=` `days`. Wstaw ją
   **bezpośrednio powyżej `Summarize`**, w tym samym miejscu co krok 3.
10. W `Summarize`, w **tym samym miejscu** co krok 4 (zaraz po pętli
    `for`, przed `return`), dodaj:
    ```go
    	expiring := ExpiringItems(inventory, 3)
    	if len(expiring) > 0 {
    		lines = append(lines, fmt.Sprintf("Expiring soon: %s", strings.Join(expiring, ", ")))
    	}
    ```
11. W `inventory_test.go` dodaj test bezpośrednio po
    `TestSummarizeListsEachItemWithQuantity`, w tym samym miejscu co krok
    5:
    ```go
    func TestExpiringItemsListsItemsWithinDays(t *testing.T) {
    	inventory := []Item{{Name: "Milk", Quantity: 2, ExpiresInDays: 1}}

    	result := ExpiringItems(inventory, 3)

    	if len(result) != 1 || result[0] != "Milk" {
    		t.Errorf("got %v, want [Milk]", result)
    	}
    }
    ```
12. Uruchom `gofmt -l .` i `go test ./... -v`, a potem utwórz na tej
    gałęzi commit ze wszystkimi zmianami.

### Java

9. W `Inventory.java` dodaj
   `public static List<String> expiringItems(List<Item> inventory, int days)`,
   zwracającą nazwy pozycji, których `expiresInDays` jest `<=` `days`.
   Wstaw ją **bezpośrednio powyżej `summarize`**, w tym samym miejscu co
   krok 3.
10. W `summarize`, w **tym samym miejscu** co krok 4 (zaraz po pętli
    `for`, przed `return`), dodaj:
    ```java
            List<String> expiring = expiringItems(inventory, 3);
            if (!expiring.isEmpty()) {
                lines.add("Expiring soon: " + String.join(", ", expiring));
            }
    ```
11. W `InventoryTest.java` dodaj test bezpośrednio po
    `summarizeListsEachItemWithQuantity`, w tym samym miejscu co krok 5.
    Importu `assertEquals` z gałęzi osoby A tutaj nie ma, bo ta gałąź
    powstała z `main`; dodaj go w ten sam sposób.
    ```java
        @Test
        void expiringItemsListsItemsWithinDays() {
            List<Inventory.Item> inventory = List.of(new Inventory.Item("Milk", 2, 1));

            List<String> result = Inventory.expiringItems(inventory, 3);

            assertEquals(List.of("Milk"), result);
        }
    ```
12. Uruchom `./gradlew test`, a potem utwórz na tej gałęzi commit ze
    wszystkimi zmianami.

**Wszystkie ścieżki**

13. Uruchom `git branch` i `git log --all --graph --oneline -5`.
    Potwierdź, że obie gałęzie istnieją i zaczynają od tego samego
    commitu. Żadna nie powinna jeszcze zawierać pracy z drugiej.

## Kryteria akceptacji

- Istnieją gałęzie `feature/low-stock-warning` i
  `feature/expiry-warning`. Każda ma dokładnie jeden commit funkcjonalny
  utworzony na tym samym commicie z `main`.
- Po przełączeniu na każdą z gałęzi testy wybranej ścieżki przechodzą.
- Plik źródłowy na żadnej gałęzi nie zawiera funkcji ani metody z
  drugiej gałęzi.

## Weryfikacja

### Python

```bash
cd examples/team-inventory/python
git branch
git log --all --graph --oneline -5
git switch feature/low-stock-warning && uv run pytest -v
git switch feature/expiry-warning && uv run pytest -v
git switch main
cd -
```

### Go

```bash
cd examples/team-inventory/go
git branch
git log --all --graph --oneline -5
git switch feature/low-stock-warning && go test ./... -v
git switch feature/expiry-warning && go test ./... -v
git switch main
cd -
```

### Java

```bash
cd examples/team-inventory/java
git branch
git log --all --graph --oneline -5
git switch feature/low-stock-warning && ./gradlew test
git switch feature/expiry-warning && ./gradlew test
git switch main
cd -
```

Obie gałęzie powinny pojawić się na liście, a oba uruchomienia testów
powinny przejść. Na `main` nadal nie ma żadnej z nowych funkcji; ich
połączenie jest zadaniem w labie 17.

## Zastanów się

- Gałąź `feature/expiry-warning` powstała z `main`, a nie z
  `feature/low-stock-warning`. Jak wpłynęłoby na późniejsze scalanie
  utworzenie jej z `feature/low-stock-warning`?
- Obie gałęzie zmieniły tę samą funkcję w tym samym miejscu. Czy Git
  widzi to teraz jako problem? Czemu tak albo czemu nie, na tym
  etapie?

## Jeśli utkniesz

### Python

- **Podpowiedź 1.** `git switch -c <name>` tworzy gałąź i od razu się
  na nią przełącza. `git switch <name>` przełącza na istniejącą gałąź.
- **Podpowiedź 2.** Przed utworzeniem każdej gałęzi funkcjonalnej upewnij
  się, że jesteś na `main`. `git branch` oznacza bieżącą gałąź znakiem
  `*`. Jeśli przez pomyłkę utworzysz B z A, B
  będzie już zawierać pracę A.
- **Podpowiedź 3.** Nowa funkcja pomocnicza (bezpośrednio powyżej
  `summarize`), i dwa wstawione bloki wewnątrz `summarize` (zaraz po
  pętli `for`) muszą trafić w dokładnie to samo miejsce w obu
  gałęziach, aby następny lab zadziałał zgodnie z opisem.

### Go

- **Podpowiedź 1.** `git switch -c <name>` tworzy gałąź i od razu się
  na nią przełącza. `git switch <name>` przełącza na istniejącą gałąź.
- **Podpowiedź 2.** Przed utworzeniem każdej gałęzi funkcjonalnej upewnij
  się, że jesteś na `main`. `git branch` oznacza bieżącą gałąź znakiem
  `*`. Jeśli przez pomyłkę utworzysz B z A, B
  będzie już zawierać pracę A.
- **Podpowiedź 3.** Nowa funkcja pomocnicza (bezpośrednio powyżej
  `Summarize`), i dwie wstawione linie wewnątrz `Summarize` (zaraz po
  pętli `for`) muszą trafić w dokładnie to samo miejsce w obu
  gałęziach, aby następny lab zadziałał zgodnie z opisem.

### Java

- **Podpowiedź 1.** `git switch -c <name>` tworzy gałąź i od razu się
  na nią przełącza. `git switch <name>` przełącza na istniejącą gałąź.
- **Podpowiedź 2.** Przed utworzeniem każdej gałęzi funkcjonalnej upewnij
  się, że jesteś na `main`. `git branch` oznacza bieżącą gałąź znakiem
  `*`. Jeśli przez pomyłkę utworzysz B z A, B
  będzie już zawierać pracę A.
- **Podpowiedź 3.** Nowa metoda pomocnicza (bezpośrednio powyżej
  `summarize`), i dwie wstawione linie wewnątrz `summarize` (zaraz po
  pętli `for`) muszą trafić w dokładnie to samo miejsce w obu
  gałęziach, aby następny lab zadziałał zgodnie z opisem.

## Co dalej

Obie funkcje istnieją, ale żadna nie wie o drugiej. W następnym labie
połączysz je i zobaczysz, dlaczego Git potrzebuje Twojej pomocy.

Przejdź do [Lab 17 — Konflikt merge'a](../17-merge-conflict/README.pl.md).
