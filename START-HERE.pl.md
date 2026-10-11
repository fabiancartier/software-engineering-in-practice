# Zacznij tutaj

[Start here (in English) →](START-HERE.md)

Ten przewodnik pomoże Ci przygotować komputer do Labu 01. Na końcu
będziesz mieć własną kopię kursu otwartą w VS Code. Nie musisz znać
terminala ani Gita. Opis samego kursu znajdziesz w głównym
[`README.pl.md`](README.pl.md).

## Co przygotujesz

- Własny **fork** kursu na koncie GitHub.
- **Klon** forka na swoim komputerze, który możesz edytować.
- Projekt otwarty w **VS Code** wraz z działającym terminalem.
- Miejsce, w którym znajdziesz pomoc, jeśli coś pójdzie nie tak.

Na tym etapie nie instaluj Pythona, Go ani Javy. Zrobisz to później,
gdy wybierzesz ścieżkę językową. Na razie wystarczy pobrać
repozytorium.

## Wybierz swój system

| Twój komputer | Przewodnik |
|---|---|
| Windows 11 | [`docs/setup/windows.pl.md`](docs/setup/windows.pl.md) |
| macOS | [`docs/setup/macos.pl.md`](docs/setup/macos.pl.md) |
| Linux | [`docs/setup/linux.pl.md`](docs/setup/linux.pl.md) |

Wybierz przewodnik dla swojego systemu i wykonaj go od początku do
końca. Zainstalujesz Git i VS Code, utworzysz fork, sklonujesz
repozytorium i otworzysz je w edytorze. Pozostałych dwóch przewodników
nie musisz czytać.

Jeśli nie chcesz instalować narzędzi na swoim komputerze, możesz użyć
[GitHub Codespaces](#github-codespaces-opcja-bez-instalacji).

## Jesteś już w trakcie?

Nie musisz zaczynać od nowa.

- **"Już zrobiłem/am fork tego repozytorium na GitHubie, ale jeszcze
  nie sklonowałem/am go."** Przejdź do kroku "Sklonuj swój fork" w
  przewodniku dla swojego systemu powyżej.
- **"Mam już lokalną kopię tego repozytorium na swoim komputerze."**
  Przejdź do
  [`docs/setup/already-have-a-fork.pl.md`](docs/setup/already-have-a-fork.pl.md).
  Dowiesz się, jak ją znaleźć, otworzyć i bezpiecznie sprawdzić jej
  stan.
- **"Coś poszło nie tak i nie jestem pewien/pewna, w jakim stanie
  jestem."** Zobacz
  [`docs/setup/troubleshooting.pl.md`](docs/setup/troubleshooting.pl.md).

## Kiedy możesz zacząć Lab 01

Możesz zaczynać, gdy spełniasz wszystkie poniższe warunki:

- [ ] Mam własny fork na GitHubie.
- [ ] Mam jego kopię na swoim komputerze.
- [ ] Wiem, gdzie na dysku znajduje się ta kopia.
- [ ] VS Code otwiera ten projekt.
- [ ] Terminal działa wewnątrz VS Code.
- [ ] Na Windowsie ten terminal to Git Bash.
- [ ] `git status` działa i nie pokazuje błędu.
- [ ] `git remote -v` pokazuje **moją nazwę użytkownika** na GitHubie, nie
  prowadzącego.
- [ ] Wiem, gdzie znaleźć instrukcje Lab 01.
- [ ] Wiem, co zrobić, jeśli później utknę.

Jeśli możesz zaznaczyć wszystkie dziesięć punktów, otwórz
[`labs/01-workstation/README.pl.md`](labs/01-workstation/README.pl.md)
i zacznij pracę.

## GitHub Codespaces (opcja bez instalacji)

Codespaces udostępnia środowisko pracy w przeglądarce. Nie musisz nic
instalować lokalnie, ale potrzebujesz stabilnego połączenia z internetem.
GitHub ogranicza miesięczne użycie bezpłatnych Codespace'ów.

1. Zrób fork tego repozytorium (przycisk **Fork**, w prawym górnym
   rogu strony GitHub), tak jak w wariancie lokalnym.
2. Na **swoim forku** otwórz **Code → Codespaces → Create codespace on
   main**.
3. Otwórz zintegrowany terminal (**Terminal → New Terminal**).
4. Otwórz
   [`labs/01-workstation/README.pl.md`](labs/01-workstation/README.pl.md)
   i zaczynaj.

Terminal w Codespace korzysta już z Basha. Punkt o Git Bash na
Windowsie nie ma tu zastosowania.
