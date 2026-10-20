# Lab 20 — Co oznacza „zrobione”?

## Sytuacja

Dwie osoby z zespołu nie mogą dojść do porozumienia. Jedna uważa, że
zadanie jest ukończone, gdy przechodzą testy. Druga chce jeszcze scalić
zmianę i zobaczyć zielony wynik CI. Każda z tych definicji obejmuje tylko
część pracy.

## Cele nauki

Po tym labie potrafisz:

- Ułożyć konkretną i sprawdzalną definicję ukończenia (Definition of
  Done) dla projektu.
- Odróżnić sprawdzalne kryterium („CI jest zielone”) od ogólnego
  stwierdzenia („kod jest dobry”).
- Ocenić wykonaną pracę na podstawie zapisanych zasad, a nie wrażenia.

## Zanim zaczniesz

- Ukończ laby 06-19 w wybranej ścieżce.
- W tym labie nie piszesz kodu. Przed rozpoczęciem przejrzyj sekcje
  „Kryteria akceptacji” w labach 11-19. Nie musisz ich odtwarzać.

## Twoje zadanie

1. Utwórz plik `labs/20-definition-of-done/definition-of-done.md` z jedną
   listą kontrolną definicji ukończenia. Lista ma zawierać 5-8 pozycji w
   postaci pól wyboru Markdown (`- [ ] pozycja`) i pasować do *każdej*
   przyszłej zmiany w `examples/team-inventory/<twój-język>`. Oprzyj ją
   na praktykach używanych w labach 06-19, takich jak testy, przegląd
   kodu, CI i dokumentacja. Nie wymagaj narzędzia używanego tylko w
   jednej ścieżce językowej.
2. Każde kryterium musi dać się jednoznacznie sprawdzić. Inna osoba
   powinna móc odpowiedzieć „tak” lub „nie” na podstawie kodu wyjścia
   polecenia, obecności pliku albo stanu pull requesta. Unikaj ogólników
   w rodzaju „kod jest czysty” lub „działa dobrze”.
3. Wybierz jeden wcześniejszy lab z zakresu 11-19. W tym samym pliku
   oceń jego faktyczny wynik według własnej listy, punkt po punkcie.
   Jeśli któreś kryterium nie jest w pełni spełnione, napisz to wprost.

## Kryteria akceptacji

- `definition-of-done.md` zawiera od 5 do 8 niezależnie sprawdzalnych
  kryteriów.
- Plik zawiera ocenę jednego wskazanego laba według każdego kryterium.
  Odpowiedzi odzwierciedlają rzeczywisty stan, a nie domyślne „tak”.

## Weryfikacja

```bash
test -f labs/20-definition-of-done/definition-of-done.md && echo "DoD exists"
grep -c '^- \[' labs/20-definition-of-done/definition-of-done.md
```

Plik powinien istnieć, a wyświetlona liczba mieścić się w zakresie 5-8.

## Zastanów się

- Czy definicja ukończenia jest pojęciem technicznym, czy raczej umową
  zespołu? Czy dwa zespoły pracujące nad podobnymi projektami mogą
  rozsądnie przyjąć inne definicje?
- Którego kryterium nie dałoby się zapisać przed ukończeniem laba 19?
  Co to mówi o zmianach definicji ukończenia wraz z rozwojem narzędzi
  projektu?

## Jeśli utkniesz

- **Podpowiedź 1.** Możesz uwzględnić testy przechodzące lokalnie i w CI,
  opis pull requesta wyjaśniający powód zmiany, ukończony przegląd kodu,
  rozwiązane uwagi, brak znaczników konfliktu oraz scalenie zmiany.
- **Podpowiedź 2.** Jeśli kryterium nie da się sprawdzić poleceniem ani
  na podstawie konkretnego stanu, przeredaguj je.
- **Podpowiedź 3.** Przykłady czytelnych list i notatek znajdziesz w
  plikach `COMPARISON.md` z laba 12 oraz `my-notes.md` z laba 15.

## Co dalej

Akt IV jest zakończony. Potrafisz bezpiecznie pracować z zespołem na
gałęziach, rozwiązywać konflikty, przeglądać kod i korzystać z CI jako
dodatkowego zabezpieczenia. Teraz oprogramowanie musi zmierzyć się ze
światem zewnętrznym, czyli innymi systemami, zapisanymi danymi i
awariami, za które nikt nie odpowiada.

Przejdź do [Lab 21 — API to kontrakt](../21-api-is-a-contract/README.pl.md).
