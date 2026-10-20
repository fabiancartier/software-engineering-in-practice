# Troubleshooting

[Po polsku →](troubleshooting.pl.md) · [← Back to Start here](../../START-HERE.md)

Find the heading below that describes the problem you see. These steps
apply across operating systems.

## I don't have administrator rights on this computer

See the Windows guide's
[No administrator rights?](windows.md#no-administrator-rights) section.
It covers installing Git and VS Code without administrator rights.
If that method is also blocked, use
[GitHub Codespaces](../../START-HERE.md#github-codespaces-the-no-install-option)
without installing tools locally.

## `choco`, `git`, `code`, or `brew`: "not recognized" or "command not found"

Close every terminal window (and VS Code, if it's open) and open a
fresh one. A program that just finished installing often doesn't show
up in a window that was already open. If it is still missing, return
to the installation step in your system's guide. Review the earlier
terminal output for an error.

## Git Bash isn't visible in VS Code's terminal (Windows)

See the Windows guide's dedicated
[I don't see Git Bash in VS Code](windows.md#i-dont-see-git-bash-in-vs-code)
section. It covers restarting VS Code and manually selecting Git Bash.

## I'm having trouble logging into GitHub

- Double-check you're using the email or username (not a typo'd
  variant) tied to your GitHub account.
- If two-factor authentication is asking for a code and you don't have
  your phone, GitHub's login page has a "Use a recovery code" or
  similar fallback link — use the recovery codes you were given when
  you set up 2FA.
- If you've truly never made a GitHub account, go to
  [github.com/join](https://github.com/join) and create one — it's
  free.

## GitHub says I already have a fork of this repository

Do not create another fork. Go to
[`already-have-a-fork.md`](already-have-a-fork.md) and follow "I
forked it on GitHub, but I don't know if I cloned it."

## I cloned the instructor's repository instead of my own fork

See the "I cloned the instructor's repository instead of my own fork"
section in your system's guide
([Windows](windows.md#i-cloned-the-instructors-repository-instead-of-my-own-fork),
[macOS](macos.md#i-cloned-the-instructors-repository-instead-of-my-own-fork),
[Linux](linux.md#i-cloned-the-instructors-repository-instead-of-my-own-fork))
— it shows a safe, non-destructive fix.

## The repository is already on my disk somewhere — I don't want to clone it again

Follow "I have a local copy already" in
[`already-have-a-fork.md`](already-have-a-fork.md). It shows how to
find the repository and check its state without cloning it again.

## I don't know what directory I'm in

Run:
```bash
pwd
```
This prints the current directory. If it is not where you expect,
return to your home directory with `cd ~` and navigate from there.

## VS Code opened the wrong folder

Choose **File → Open Folder…** again and select the cloned repository.
The Explorer panel should show `labs`, `examples`, and `scripts`. If it
does not, you may have opened a parent directory such as `projects` or
a subdirectory such as `labs`.

## "command not found" for something other than git/code/choco/brew

Check that you are using the terminal from your system's guide: Git
Bash on Windows or the default terminal on macOS and Linux. Commands
available in one shell may not exist in another. If unsure, close the
terminal and reopen it by following the guide.

## `labs/`, `examples/`, or `scripts/` is missing from the project

This usually means VS Code or the terminal is open in the wrong folder,
or the clone did not finish. Run:
```bash
pwd
ls
```
If `ls` does not show `labs`, `examples`, and `scripts`, use `cd` to
enter the repository folder or reopen it in VS Code with
**File → Open Folder…**.

## My first `git push` isn't working

See [`github-auth.md`](github-auth.md). It explains first-push
authentication and what to do if no browser window appears.

## Git Bash says something about line endings, or a `.sh` script won't run (Windows)

If a script fails with something like `$'\r': command not found` or
similar, the file likely has Windows-style line endings. This course's
own scripts are already committed with the correct line endings, so
this is unlikely to affect anything in `labs/`, `examples/`, or
`scripts/` as cloned — if you see this on a file you didn't create or
edit yourself, report it rather than trying to fix the file (see
below). If it's a file you created yourself in a plain text editor
other than VS Code, reopen and save it from VS Code instead, which
uses the correct line endings by default for this repository.

## If you're still stuck

Report the problem to your instructor with these five things:

1. Your operating system (Windows 11, macOS, or which Linux
   distribution).
2. Which step number or section you were on.
3. The exact command you ran.
4. The exact error message you saw (copy the text, or a screenshot).
5. What you expected to happen instead.

**Never include a password, personal access token, or any other secret
in this report** — not in the text, not in a screenshot. If a
screenshot would show one, crop or redact it first.
