# Start here

[Zacznij tutaj (po polsku) →](START-HERE.pl.md)

This guide will help you prepare your computer for Lab 01. By the end,
you will have your own copy of the course open in VS Code. You do not
need experience with the terminal or Git. See the main
[`README.md`](README.md) for an overview of the course itself.

## What you will set up

- Your own **fork** of the course on GitHub.
- A **clone** of the fork on your computer that you can edit.
- The project open in **VS Code** with a working terminal.
- A place to look for help if something goes wrong.

Do not install Python, Go, or Java yet. You will do that after choosing
a language in Lab 05. For now, you only need the repository.

## Pick your system

| Your computer | Guide |
|---|---|
| Windows 11 | [`docs/setup/windows.md`](docs/setup/windows.md) |
| macOS | [`docs/setup/macos.md`](docs/setup/macos.md) |
| Linux | [`docs/setup/linux.md`](docs/setup/linux.md) |

Choose the guide for your system and follow it from beginning to end.
You will install Git and VS Code, create a fork, clone the repository,
and open it in the editor. You do not need to read the other two
guides.

If you prefer not to install tools on your computer, use
[GitHub Codespaces](#github-codespaces-the-no-install-option).

## Already partway through this?

You don't need to start over.

- **"I already forked this repository on GitHub, but haven't cloned
  it yet."** Skip to the "Clone your fork" step in your system's
  guide above.
- **"I already have a local copy of this repository on my computer."**
  Go to
  [`docs/setup/already-have-a-fork.md`](docs/setup/already-have-a-fork.md).
  It explains how to find the copy, open it, and check its state
  safely.
- **"Something went wrong and I'm not sure what state I'm in."** See
  [`docs/setup/troubleshooting.md`](docs/setup/troubleshooting.md).

## When you're ready for Lab 01

You can start once every item below is true:

- [ ] I have my own fork on GitHub.
- [ ] I have a copy of it on my computer.
- [ ] I know where on my disk that copy lives.
- [ ] VS Code opens that project.
- [ ] A terminal works inside VS Code.
- [ ] On Windows, that terminal is Git Bash.
- [ ] `git status` works and doesn't show an error.
- [ ] `git remote -v` shows **my own** GitHub username, not the
  instructor's.
- [ ] I know where to find Lab 01's instructions.
- [ ] I know what to do if I get stuck later.

If you can check all ten items, open
[`labs/01-workstation/README.md`](labs/01-workstation/README.md) and
start.

## GitHub Codespaces (the no-install option)

Codespaces provides a development environment in your browser. You do
not need to install anything locally, but you do need a stable internet
connection. GitHub limits monthly use on the free tier.

1. Fork this repository (**Fork** button, top right of the GitHub
   page), the same as the local path.
2. On **your fork**, open **Code → Codespaces → Create codespace on
   main**.
3. Open the integrated terminal (**Terminal → New Terminal**).
4. Open [`labs/01-workstation/README.md`](labs/01-workstation/README.md)
   and begin.

The Codespace terminal already uses Bash, so the Git Bash checklist
item for Windows does not apply.
