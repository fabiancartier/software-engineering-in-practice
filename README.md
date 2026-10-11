# Software Engineering in Practice

[Czytaj po polsku →](README.pl.md)

**New here? [Start here](START-HERE.md).** The guide will help you set
up a Windows, macOS, or Linux computer and open this repository. You
do not need prior experience with the terminal, Git, or GitHub.

[![Course Health](https://github.com/michalmaj/software-engineering-in-practice/actions/workflows/course-health.yml/badge.svg)](https://github.com/michalmaj/software-engineering-in-practice/actions/workflows/course-health.yml)
[![Python 3.13](https://img.shields.io/badge/Python-3.13-blue)](https://www.python.org/)
[![Go 1.27](https://img.shields.io/badge/Go-1.27-00ADD8)](https://go.dev/)
[![Java 21](https://img.shields.io/badge/Java-21-ED8B00)](https://adoptium.net/)
[![30 Labs](https://img.shields.io/badge/30%20Labs-orange)](#course-map)
[![Bilingual EN | PL](https://img.shields.io/badge/EN%20%7C%20PL-9cf)](README.pl.md)
[![License: MIT](https://img.shields.io/badge/code-MIT-yellow)](LICENSE)
[![License: CC BY 4.0](https://img.shields.io/badge/content-CC%20BY%204.0-lightgrey)](LICENSE-CONTENT.md)
[![Codespaces — ready](https://img.shields.io/badge/Codespaces%20%E2%80%94%20ready-success)](#github-codespaces)

> From the terminal to teamwork. Learn to build software that can
> change safely.

## What this course is

This hands-on software engineering course consists of 30
ninety-minute labs. Each task builds on the same project and follows
from a problem encountered earlier. The course is intended for
university students. It requires basic programming knowledge in
Python, Go, or Java, but no professional experience.

## Core idea

> Programming is about making a program work. Software engineering is
> also about making it safe to change, understand, review, reproduce,
> operate, and hand over.

Each lab starts with a problem created by the previous stage of the
project. You encounter the problem first, then learn its name and how
to address it.

## Course journey

```text
workstation → terminal → Git → project → tests → design →
collaboration → CI → APIs, data, and failures → release →
team project → handover
```

| Act | Labs  | Theme                              |
|-----|-------|-------------------------------------|
| I   | 01-05 | I am a developer                    |
| II  | 06-10 | Code is not yet a project           |
| III | 11-15 | Software must survive change        |
| IV  | 16-20 | You do not work alone               |
| V   | 21-25 | The system lives in a larger world  |
| VI  | 26-30 | You are the engineering team        |

## Start here

New to terminals, Git, or GitHub? Begin with
[`START-HERE.md`](START-HERE.md). It links to separate instructions for
Windows, macOS, and Linux and covers installing Git and VS Code,
forking this repository, and cloning your fork.

Already comfortable with Git and a terminal? The short version:

1. Fork this repository (**Fork** button, top right of the GitHub
   page).
2. Clone your fork and open it in VS Code. See
   [`START-HERE.md`](START-HERE.md) for commands specific to your
   system.
3. Open [`labs/01-workstation/README.md`](labs/01-workstation/README.md)
   and begin.

Prefer a browser-based environment instead of installing anything
locally? See [GitHub Codespaces](#github-codespaces) below.

## Local setup (the default path)

Windows, macOS, and Linux are equally supported.
[`START-HERE.md`](START-HERE.md) and the guides under
[`docs/setup/`](docs/setup/) cover each system step by step.

You only need Git to download the repository. Docker Desktop, WSL, and
GitHub Desktop are not required. Install Python, Go, or a JDK when you
choose a language in Lab 05. You do not need all three toolchains.

Once you have chosen a language, you will need its toolchain:

| Tool   | Required version |
|--------|-------------------|
| Python | 3.13.x             |
| `uv`   | 0.11.21            |
| Go     | 1.27.x             |
| JDK    | 21                 |

You do not need to install Gradle separately. The Java starters include
the Gradle Wrapper (`./gradlew`), so a JDK is enough. Run
`./scripts/check-environment.sh` at any time to check your tool
versions. This becomes useful in later labs but is not required for
Lab 01.

## GitHub Codespaces

If you prefer not to install anything locally, you can work in
Codespaces. The environment runs in your browser, but it requires a
stable internet connection and is subject to GitHub's monthly free-tier
limits.

- **Create it from your own fork**, not the original course repository.
  You need write access for later labs that have you commit and push.
  Creating a Codespace on the original repository instead of your fork
  is the single most common setup mistake; double-check the repository
  name in the URL before continuing.
- **Open the terminal** with **Terminal → New Terminal** once the
  Codespace finishes initializing.
- **The toolchain is already installed** — Python, Go, and the JDK all
  come preconfigured. Running `./scripts/check-environment.sh` is
  optional, useful only if you want to confirm versions match the table
  above; it's not required to start Lab 01.
- **Stop Codespaces you're not using** from
  [github.com/codespaces](https://github.com/codespaces) (or let them
  auto-suspend). Codespaces has monthly usage limits; stopping one
  doesn't delete your work.

1. Fork this repository (**Fork** button, top right of the GitHub page).
2. On **your fork**, open **Code → Codespaces → Create codespace on
   main**.
3. Open the integrated terminal (**Terminal → New Terminal**).
4. Open [`labs/01-workstation/README.md`](labs/01-workstation/README.md)
   and begin.

## Languages and tools

| Ecosystem | Toolchain              | Tests                    |
|-----------|-------------------------|---------------------------|
| Python    | `uv`                    | `pytest`                  |
| Go        | standard Go tooling      | `go test`                 |
| Java      | JDK 21 + Gradle Wrapper  | JUnit, via `./gradlew test` |

Labs 01-04 are shared by everyone. In Lab 05 you choose one of three
equal, **fully supported** paths: Python, Go, or Java. You use that
language through the Act VI capstone. Each path has the same learning
outcomes, working starters, and language-specific commands for running
code and tests.

Lab 14 compares the same idea in all three languages, but you still
write code only in the language you chose. The language is a tool; the
subject is software engineering. One toolchain is enough for the
entire course.

## How the labs work

Every lab follows a similar structure: **Story → Learning objectives →
Before you start → Your task → Acceptance criteria → Verification →
Think about it → If you get stuck → What's next.** Some labs add a
timing note, an "All tracks" section, or an architecture decision
record. The materials work both for self-study and for classroom use.

## Course map

| Lab | Title | Lab | Title |
|-----|-------|-----|-------|
| [01](labs/01-workstation/README.md) | Welcome to your workstation | [16](labs/16-parallel-branches/README.md) | Branches exist because work happens in parallel |
| [02](labs/02-terminal/README.md) | The terminal is a development tool | [17](labs/17-merge-conflict/README.md) | The merge conflict |
| [03](labs/03-inherited-repository/README.md) | You inherited a repository | [18](labs/18-pull-requests-and-review/README.md) | Pull requests and code review |
| [04](labs/04-local-vs-remote/README.md) | Local is not remote | [19](labs/19-repository-checks-itself/README.md) | The repository should check itself |
| [05](labs/05-works-on-my-machine/README.md) | "It works on my machine" | [20](labs/20-definition-of-done/README.md) | What does "done" mean? |
| [06](labs/06-from-script-to-project/README.md) | From script to project | [21](labs/21-api-is-a-contract/README.md) | An API is a contract |
| [07](labs/07-automated-tests/README.md) | How do we know it works? | [22](labs/22-data-outlives-code/README.md) | Code changed, old data remained |
| [08](labs/08-bug-report/README.md) | A bug report arrives | [23](labs/23-outside-world-fails/README.md) | The outside world fails |
| [09](labs/09-automated-checks/README.md) | Machines can check boring things | [24](labs/24-production-says-it-doesnt-work/README.md) | Production says "it does not work" |
| [10](labs/10-one-way-to-check/README.md) | One obvious way to check the project | [25](labs/25-release-and-compatibility/README.md) | Release and compatibility |
| [11](labs/11-changed-requirements/README.md) | The client changed their mind | [26](labs/26-project-kickoff/README.md) | Project kickoff |
| [12](labs/12-change-surface/README.md) | Where should this change go? | [27](labs/27-development-iteration/README.md) | Development iteration |
| [13](labs/13-refactoring-safety-net/README.md) | Refactoring with a safety net | [28](labs/28-change-request/README.md) | Change request |
| [14](labs/14-one-contract-three-languages/README.md) | One contract, three languages | [29](labs/29-production-incident/README.md) | Production incident |
| [15](labs/15-patterns-without-worship/README.md) | Patterns without pattern worship | [30](labs/30-handover/README.md) | Handover |

## Repository health

Run `./scripts/check-course.sh` to run the same checks this
repository's own CI runs on every push and pull request: repository
structure, EN/PL parity, and every example project's tests, syntax, and
lockfiles. This repository holds itself to the same practices it
teaches.

## Contributing / reporting problems

Found a bug, unclear instruction, or something that does not work in
your environment? Please open an issue.

## Licensing

This repository is dual-licensed:

- **Code** — source code, tests, scripts, configuration, CI/CD
  workflows, starter projects, and code snippets embedded in lab
  Markdown files — is licensed under the [MIT License](LICENSE).
- **Instructional content** — lab READMEs, task descriptions,
  narrative, questions, hints, and diagrams — is licensed under
  [Creative Commons Attribution 4.0 International](LICENSE-CONTENT.md)
  (CC BY 4.0).

Third-party materials included in this repository, if any, keep their
own original copyright and licensing terms.

## Instructor / author

Created and maintained by Michał Maj.
