# Software Engineering in Practice

[Read this in English →](README.md)

**Jesteś tu pierwszy raz? [Zacznij tutaj](START-HERE.pl.md).** Ten
przewodnik pomoże Ci przygotować komputer z Windows, macOS albo Linuksem
i otworzyć repozytorium. Nie musisz wcześniej znać terminala, Gita ani
GitHuba.

[![Course Health](https://github.com/michalmaj/software-engineering-in-practice/actions/workflows/course-health.yml/badge.svg)](https://github.com/michalmaj/software-engineering-in-practice/actions/workflows/course-health.yml)
[![Python 3.13](https://img.shields.io/badge/Python-3.13-blue)](https://www.python.org/)
[![Go 1.27](https://img.shields.io/badge/Go-1.27-00ADD8)](https://go.dev/)
[![Java 21](https://img.shields.io/badge/Java-21-ED8B00)](https://adoptium.net/)
[![30 Labs](https://img.shields.io/badge/30%20Labs-orange)](#mapa-kursu)
[![Bilingual EN | PL](https://img.shields.io/badge/EN%20%7C%20PL-9cf)](README.md)
[![License: MIT](https://img.shields.io/badge/code-MIT-yellow)](LICENSE)
[![License: CC BY 4.0](https://img.shields.io/badge/content-CC%20BY%204.0-lightgrey)](LICENSE-CONTENT.md)
[![Codespaces — ready](https://img.shields.io/badge/Codespaces%20%E2%80%94%20ready-success)](#github-codespaces)

> Od terminala do pracy zespołowej. Ucz się budować oprogramowanie,
> które można bezpiecznie zmieniać.

## Czym jest ten kurs

To praktyczny kurs inżynierii oprogramowania złożony z 30
dziewięćdziesięciominutowych laboratoriów. Kolejne zadania rozwijają
jeden projekt i wynikają z problemów napotkanych wcześniej. Kurs jest
przeznaczony dla studentów. Nie wymaga doświadczenia zawodowego,
wystarczą podstawy wybranego języka: Pythona, Go albo Javy.

## Główna idea

> W programowaniu chodzi o to, żeby program działał. W inżynierii
> oprogramowania również o to, żeby można go było bezpiecznie zmieniać,
> zrozumieć, sprawdzić, odtworzyć i przekazać kolejnej osobie.

Każde laboratorium zaczyna się od problemu, który pojawił się na
poprzednim etapie projektu. Najpierw go napotkasz, potem poznasz jego
nazwę i sposób rozwiązania.

## Podróż przez kurs

```text
stanowisko pracy → terminal → Git → projekt → testy → projektowanie →
współpraca → CI → API, dane i awarie → wydanie →
projekt zespołowy → przekazanie
```

| Akt | Laby  | Temat                                      |
|-----|-------|---------------------------------------------|
| I   | 01-05 | Jestem developerem                          |
| II  | 06-10 | Kod to jeszcze nie projekt                  |
| III | 11-15 | Oprogramowanie musi przetrwać zmianę        |
| IV  | 16-20 | Nie pracujesz w pojedynkę                   |
| V   | 21-25 | System żyje w większym świecie              |
| VI  | 26-30 | Jesteście zespołem inżynierskim             |

## Zacznij tutaj

Nie znasz jeszcze terminala, Gita albo GitHuba? Zacznij od
[`START-HERE.pl.md`](START-HERE.pl.md). Znajdziesz tam osobne instrukcje
dla Windows, macOS i Linuksa: instalację Gita i VS Code, utworzenie forka
oraz sklonowanie repozytorium.

Już czujesz się swobodnie z Gitem i terminalem? Krótka wersja:

1. Zrób fork tego repozytorium (przycisk **Fork** w prawym górnym rogu
   strony GitHub).
2. Sklonuj swój fork i otwórz go w VS Code. Odpowiednie polecenia dla
   swojego systemu znajdziesz w
   [`START-HERE.pl.md`](START-HERE.pl.md).
3. Otwórz
   [`labs/01-workstation/README.pl.md`](labs/01-workstation/README.pl.md)
   i zaczynaj.

Wolisz środowisko w przeglądarce zamiast instalować coś lokalnie? Zobacz
[GitHub Codespaces](#github-codespaces) poniżej.

## Konfiguracja lokalna (ścieżka domyślna)

Windows, macOS i Linux są wspierane na takich samych zasadach.
[`START-HERE.pl.md`](START-HERE.pl.md) oraz przewodniki w
[`docs/setup/`](docs/setup/) opisują konfigurację każdego z tych
systemów krok po kroku.

Do pobrania repozytorium potrzebujesz tylko Gita. Docker Desktop, WSL i
GitHub Desktop nie są wymagane. Pythona, Go albo JDK zainstalujesz
dopiero wtedy, gdy wybierzesz ścieżkę językową. Na początku kursu nie
musisz instalować wszystkich trzech zestawów narzędzi.

Po wybraniu języka będzie Ci potrzebny odpowiedni zestaw narzędzi:

| Narzędzie | Wymagana wersja      |
|-----------|------------------------|
| Python    | 3.13.x                 |
| `uv`      | 0.11.21                 |
| Go        | 1.27.x                 |
| JDK       | 21                     |

Nie musisz osobno instalować Gradle. Startery w Javie zawierają Gradle
Wrapper (`./gradlew`), dlatego lokalnie wystarczy JDK. Wersje narzędzi
możesz w każdej chwili sprawdzić poleceniem
`./scripts/check-environment.sh`. Przyda się ono w późniejszych labach,
ale nie jest potrzebne w Labie 01.

## GitHub Codespaces

Jeśli nie chcesz niczego instalować lokalnie, możesz pracować w
Codespaces. Środowisko działa w przeglądarce, ale wymaga stałego
połączenia z internetem. Pamiętaj też o miesięcznym limicie bezpłatnego
użycia.

- **Utwórz Codespace na swoim forku**, nie w oryginalnym repozytorium
  kursu. W kolejnych labach będziesz zapisywać i wysyłać zmiany, więc
  potrzebujesz praw do zapisu. Przed rozpoczęciem sprawdź nazwę
  repozytorium w adresie URL.
- **Otwórz terminal** przez **Terminal → New Terminal**, gdy Codespace
  skończy się inicjalizować.
- **Narzędzia są już zainstalowane.** Python, Go i JDK są gotowe do
  użycia. Możesz sprawdzić ich wersje poleceniem
  `./scripts/check-environment.sh`, ale nie jest to wymagane w Labie 01.
- **Zatrzymuj nieużywane Codespace'y** z poziomu
  [github.com/codespaces](https://github.com/codespaces) (albo pozwól
  im automatycznie się wyłączyć). Zatrzymanie środowiska nie usuwa
  Twojej pracy.

1. Zrób fork tego repozytorium (przycisk **Fork** w prawym górnym rogu
   strony GitHub).
2. Na **swoim forku** otwórz **Code → Codespaces → Create codespace on
   main**.
3. Otwórz zintegrowany terminal (**Terminal → New Terminal**).
4. Otwórz
   [`labs/01-workstation/README.pl.md`](labs/01-workstation/README.pl.md)
   i zaczynaj.

## Języki i narzędzia

| Ekosystem | Toolchain                | Testy                       |
|-----------|----------------------------|-------------------------------|
| Python    | `uv`                       | `pytest`                      |
| Go        | standardowe narzędzia Go    | `go test`                     |
| Java      | JDK 21 + Gradle Wrapper     | JUnit, przez `./gradlew test` |

Laby 01-04 są wspólne dla wszystkich. W Labie 05 wybierasz jedną z
trzech równorzędnych ścieżek: Python, Go albo Java. Trzymasz się jej aż
do projektu końcowego w Akcie VI. Każda ścieżka ma te same cele,
działające startery oraz własne polecenia do uruchamiania kodu i testów.

W Labie 14 porównasz rozwiązania we wszystkich trzech językach, ale kod
nadal napiszesz tylko w wybranym języku. Język jest tu narzędziem, a
tematem kursu pozostaje inżynieria oprogramowania. Przez cały kurs
wystarczy Ci jeden zestaw narzędzi.

## Jak działają laby

Każdy lab ma podobną strukturę: **Sytuacja → Cele nauki → Zanim
zaczniesz → Twoje zadanie → Kryteria akceptacji → Weryfikacja →
Zastanów się → Jeśli utkniesz → Co dalej.** W razie potrzeby pojawiają
się dodatkowe sekcje, na przykład wskazówki dotyczące czasu albo zapis
decyzji architektonicznej w Labie 26. Materiały nadają się zarówno do
samodzielnej nauki, jak i do pracy podczas zajęć.

## Mapa kursu

| Lab | Tytuł | Lab | Tytuł |
|-----|-------|-----|-------|
| [01](labs/01-workstation/README.pl.md) | Witaj na swoim stanowisku pracy | [16](labs/16-parallel-branches/README.pl.md) | Gałęzie istnieją, bo praca dzieje się równolegle |
| [02](labs/02-terminal/README.pl.md) | Terminal jako narzędzie pracy | [17](labs/17-merge-conflict/README.pl.md) | Konflikt scalania |
| [03](labs/03-inherited-repository/README.pl.md) | Dziedziczysz repozytorium | [18](labs/18-pull-requests-and-review/README.pl.md) | Pull requesty i code review |
| [04](labs/04-local-vs-remote/README.pl.md) | Lokalne to nie zdalne | [19](labs/19-repository-checks-itself/README.pl.md) | Repozytorium powinno sprawdzać się samo |
| [05](labs/05-works-on-my-machine/README.pl.md) | "Działa na moim komputerze" | [20](labs/20-definition-of-done/README.pl.md) | Co oznacza "zrobione"? |
| [06](labs/06-from-script-to-project/README.pl.md) | Od skryptu do projektu | [21](labs/21-api-is-a-contract/README.pl.md) | API to kontrakt |
| [07](labs/07-automated-tests/README.pl.md) | Skąd wiemy, że to działa? | [22](labs/22-data-outlives-code/README.pl.md) | Kod się zmienił, stare dane zostały |
| [08](labs/08-bug-report/README.pl.md) | Nadchodzi zgłoszenie błędu | [23](labs/23-outside-world-fails/README.pl.md) | Świat zewnętrzny zawodzi |
| [09](labs/09-automated-checks/README.pl.md) | Maszyny mogą sprawdzać nudne rzeczy | [24](labs/24-production-says-it-doesnt-work/README.pl.md) | Produkcja mówi "to nie działa" |
| [10](labs/10-one-way-to-check/README.pl.md) | Jeden oczywisty sposób sprawdzania projektu | [25](labs/25-release-and-compatibility/README.pl.md) | Wydanie i kompatybilność |
| [11](labs/11-changed-requirements/README.pl.md) | Klient zmienił zdanie | [26](labs/26-project-kickoff/README.pl.md) | Kickoff projektu |
| [12](labs/12-change-surface/README.pl.md) | Gdzie powinna trafić ta zmiana? | [27](labs/27-development-iteration/README.pl.md) | Iteracja rozwojowa |
| [13](labs/13-refactoring-safety-net/README.pl.md) | Refaktoryzacja z siatką bezpieczeństwa | [28](labs/28-change-request/README.pl.md) | Zmiana wymagań |
| [14](labs/14-one-contract-three-languages/README.pl.md) | Jeden kontrakt, trzy języki | [29](labs/29-production-incident/README.pl.md) | Incydent produkcyjny |
| [15](labs/15-patterns-without-worship/README.pl.md) | Wzorce bez kultu wzorców | [30](labs/30-handover/README.pl.md) | Handover |

## Zdrowie repozytorium

Polecenie `./scripts/check-course.sh` uruchamia te same kontrole co CI
repozytorium: sprawdza strukturę materiałów, zgodność wersji EN/PL,
składnię, pliki blokad oraz testy przykładowych projektów. Repozytorium
stosuje zasady, których uczy.

## Zgłaszanie problemów

Widzisz błąd, niejasną instrukcję albo coś, co nie działa w Twoim
środowisku? Otwórz issue.

## Licencjonowanie

To repozytorium ma podwójną licencję:

- **Kod**, czyli kod źródłowy, testy, skrypty, konfiguracja, procesy
  CI/CD, startery i fragmenty kodu w materiałach, jest objęty
  [licencją MIT](LICENSE).
- **Treść dydaktyczna**, czyli instrukcje, opisy zadań, pytania,
  podpowiedzi i diagramy, jest objęta licencją
  [Creative Commons Attribution 4.0 International](LICENSE-CONTENT.md)
  (CC BY 4.0).

Materiały stron trzecich zawarte w tym repozytorium, jeśli takie
istnieją, zachowują własne oryginalne prawa autorskie i warunki
licencyjne.

## Autor

Stworzone i utrzymywane przez Michała Maja.
