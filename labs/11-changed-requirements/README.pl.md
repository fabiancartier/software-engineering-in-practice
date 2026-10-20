# Lab 11 — Klient zmienił zdanie

## Sytuacja

Właściciel restauracji mówi: „Chcemy, żeby kelnerzy mogli wpisać kod
rabatowy podczas finalizowania zamówienia”. To całe zlecenie. Nie
wiadomo, jakie kody są dostępne, jakie dają rabaty, czy łączą się z
rabatem lojalnościowym ani co zrobić, gdy ktoś pomyli się przy wpisywaniu.

## Cele nauki

Po tym labie potrafisz:

- Zamienić niejasne zlecenie na listę konkretnych pytań doprecyzowujących.
- Przełożyć ustalone wymaganie na konkretne przykłady danych wejściowych
  i oczekiwanych wyników.
- Wyjaśnić, dlaczego „co się dzieje przy niepoprawnych danych wejściowych”
  samo w sobie jest wymaganiem, a nie szczegółem implementacji.

## Zanim zaczniesz

- Laby 06-10 ukończone.
- W tym labie nie piszesz kodu. Zajmujesz się wyłącznie analizą wymagań.

## Twoje zadanie

1. Zanim przeczytasz dalej, zapisz co najmniej pięć pytań, które warto
   zadać właścicielowi restauracji o kody rabatowe.
   Umieść je w
   `labs/11-changed-requirements/my-clarifying-questions.md`. Zastanów
   się, jakie kody istnieją, jaki rabat daje każdy z nich, czy łączą się
   z rabatem lojalnościowym, co zrobić z nierozpoznanym kodem i czy w
   jednym zamówieniu można użyć kilku kodów.
2. Teraz przeczytaj uzgodnioną specyfikację. Właśnie to właściciel miał
   na myśli, gdy ktoś zadał mu właściwe pytania.

   - Istnieją dokładnie dwa kody rabatowe: `SAVE10` i `SAVE5`.
   - `SAVE10` obniża o 10% kwotę, która pozostaje *po* zastosowaniu
     istniejącego rabatu lojalnościowego.
   - `SAVE5` obniża tę samą kwotę o 5 dolarów.
   - W jednym zamówieniu można użyć najwyżej jednego kodu rabatowego.
   - Nierozpoznany kod oznacza błąd. System musi odrzucić zamówienie,
     zamiast po cichu naliczyć pełną cenę.
3. W tym samym pliku notatek dodaj tabelę z co najmniej czterema
   konkretnymi przykładami. Uwzględnij zamówienie z `SAVE10`, zamówienie
   z `SAVE5`, zamówienie bez kodu oraz zamówienie z nieistniejącym
   kodem. Dla każdego przykładu podaj oczekiwaną sumę albo błąd.

## Kryteria akceptacji

- `my-clarifying-questions.md` zawiera co najmniej pięć odrębnych pytań
  doprecyzowujących, napisanych *przed* przeczytaniem ustalonej
  specyfikacji.
- Ten sam plik zawiera tabelę przykładów z co najmniej czterema
  wierszami obejmującymi scenariusze z kroku 3.

## Weryfikacja

```bash
test -f labs/11-changed-requirements/my-clarifying-questions.md && echo "notes exist"
grep -cE '^[[:space:]]*([0-9]+\.|[-*])[[:space:]]+\S' labs/11-changed-requirements/my-clarifying-questions.md
```

(Drugie polecenie liczy elementy listy zapisane w *dowolnym* stylu
Markdown: jako lista numerowana, z `-` albo z `*`. W tym labie liczą się
dobre pytania, a nie wybrana składnia listy.)

*Treści* analizy wymagań nie da się sprawdzić automatycznie. Przeczytaj
notatki jeszcze raz i upewnij się, że każdy przykład ma jeden
jednoznaczny wynik.

## Zastanów się

- Na ile z Twoich pięciu pytań odpowiada uzgodniona specyfikacja? Co
  należałoby zrobić z pozostałymi pytaniami w prawdziwym projekcie?
- „Nierozpoznany kod to błąd” samo w sobie jest decyzją projektową, a
  nie oczywistym domyślnym zachowaniem. Co zmieniłoby się w zachowaniu
  systemu, gdyby właściciel powiedział zamiast tego „po prostu ignoruj
  kody, których nie rozpoznajemy”?

## Jeśli utkniesz

- **Podpowiedź 1.** Na dobre pytanie doprecyzowujące można odpowiedzieć
  konkretnym faktem, a nie „to zależy”. „Czy kody rabatowe wygasają?”
  jest lepsze niż „Jak powinny działać kody rabatowe?”.
- **Podpowiedź 2.** W tabeli użyj konkretnych liczb: rzeczywistej sumy
  zamówienia, konkretnego kodu i oczekiwanego wyniku. Unikaj opisów w
  rodzaju „jakieś zamówienie”.
- **Podpowiedź 3.** Jeśli nie masz pewności, czy `SAVE10` stosuje się
  przed czy po rabacie lojalnościowym, przeczytaj ponownie
  uzgodnioną specyfikację. Jest to w niej napisane wprost.

Zanim przejdziesz dalej, utwórz commit ze wszystkim z tego laba i
wypchnij go do repozytorium
(`git add -A && git commit -m "..."; git push`). Nic później jeszcze
nie wymaga czystego drzewa roboczego, ale Akt IV (od Lab 16) już tak.
Warto od razu wyrobić sobie ten nawyk.

## Co dalej

Wiesz już, co trzeba zbudować. Teraz musisz ustalić, gdzie w kodzie
powinna trafić ta zmiana.

Przejdź do [Lab 12 — Gdzie powinna trafić ta zmiana?](../12-change-surface/README.pl.md).
