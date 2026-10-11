# Twój pierwszy push do GitHuba

[In English →](github-auth.md) · [← Wróć do Zacznij tutaj](../../START-HERE.pl.md)

Klonowanie i czytanie repozytorium nie wymaga logowania. Przy pierwszym
`git push` musisz jednak potwierdzić swoją tożsamość na GitHubie.
Wróć do tej strony, gdy lab po raz pierwszy poprosi Cię o wysłanie
zmian.

## Czego NIE robić

- **Nie wpisuj hasła do konta GitHub** w terminalu. GitHub nie
  przyjmuje haseł konta podczas operacji Gita przez HTTPS. Jeśli pojawi
  się taka prośba, przejdź do sekcji
  [Jeśli utkniesz](#jeśli-utkniesz).
- **Nigdy nie wklejaj tokenu** (czegoś zaczynającego się na `ghp_` albo
  podobnie) do README tego repozytorium, do issue na GitHubie, do
  opisu commita ani wiadomości do prowadzącego. Token ujawniony innej
  osobie należy unieważnić na GitHubie (**Settings → Developer settings
  → Personal access tokens**) i zastąpić nowym.

## Co zrobić zamiast tego

### Najprostsza metoda: logowanie w przeglądarce

Przy pierwszym `git push` VS Code lub Git zwykle otworzy stronę
logowania w przeglądarce. Zaloguj się do GitHuba, przejdź
uwierzytelnianie dwuetapowe, jeśli jest włączone, i kliknij
**Authorize**. Następnie wróć do terminala. Wysyłanie zmian powinno być
kontynuowane automatycznie.

Obsługuje to **Git Credential Manager**. Jest dołączony do Gita dla
Windows i często dostępny również na macOS i Linuksie. Zapamiętuje
wynik logowania, dlatego na tym komputerze zwykle wystarczy zrobić to
raz.

### Jeśli żadne okno przeglądarki się nie pojawia

1. Spróbuj push jeszcze raz:
   ```bash
   git push
   ```
2. Jeśli Git wyświetli `Username for 'https://github.com':`, wpisz
   swoją nazwę użytkownika z GitHuba i naciśnij
   Enter.
3. Jeśli potem wypisuje `Password for
   'https://<username>@github.com':`, to **nie** prosi o hasło Twojego
   konta. W tym miejscu podaj **personal access token**, czyli osobne
   poświadczenie, które można później unieważnić. Aby je utworzyć:
   - W przeglądarce przejdź do **GitHub → Settings → Developer settings
     → Personal access tokens → Tokens (classic) → Generate new
     token**.
   - Daj mu nazwę, którą rozpoznasz (np.
     `software-engineering-in-practice`), datę wygaśnięcia (30-90 dni
     wystarczy na kurs) i zaznacz zakres **repo**.
   - Kliknij **Generate token** i od razu go skopiuj. GitHub nie pokaże
     go ponownie.
4. Wklej token w polu hasła w terminalu i naciśnij Enter. Podczas
   wklejania nie zobaczysz żadnych znaków.

Po udanym wysłaniu terminal pokaże postęp zakończony komunikatem
podobnym do `main -> main`.

## Jeśli utkniesz

- **"Prosi o hasło, a token też nie zadziałał."** Sprawdź, czy token
  został skopiowany w całości, nie zawiera dodatkowych spacji i nie
  wygasł. W razie wątpliwości wygeneruj nowy.
- **"Nie widzę okna przeglądarki ani pytania w terminalu."** Naciśnij
  `Ctrl+C`, otwórz nowy terminal i ponownie uruchom `git push`.
- **Problem nadal występuje?** W
  [`troubleshooting.pl.md`](troubleshooting.pl.md) znajdziesz wskazówki
  dotyczące zgłoszenia. Nie dołączaj do niego hasła, tokenu ani innych
  poufnych danych.
