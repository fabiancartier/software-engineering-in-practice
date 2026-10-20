# Lab 02 — The terminal is a development tool

## Story

Yesterday you learned to navigate your workstation. Today you will work
without a mouse: copy files, search their contents, combine commands,
and control a process that runs until you stop it.

## Learning objectives

After this lab you should be able to:

- Copy, move, and remove files and directories from the shell.
- Search file contents and find files by name.
- Redirect output to a file and pipe the output of one command into
  another.
- Start a long-running process, observe it, and stop it with `Ctrl+C`.

## Before you start

- Lab 01 complete (you can navigate and create files from the terminal).
- You can start in your home directory or in the repository checkout.

## Your task

1. Inside `~/lab01-notes/`, copy `findings.txt` to `findings.bak.txt`.
2. Create a directory `~/lab02-notes/` and move `findings.bak.txt` into it.
3. Search `findings.txt` for the line containing `whoami` using `grep`, and
   redirect that single matching line into a new file
   `~/lab02-notes/whoami-line.txt`.
4. Use `find` to locate every file named `findings.txt` under the two
   directories you have created, `~/lab01-notes/` and
   `~/lab02-notes/`. Pipe the result into `wc -l` to count the matches:
   `find ~/lab01-notes ~/lab02-notes -name findings.txt | wc -l`.
   Expect `1`. The original is still in `lab01-notes`, while the copy
   from step 1 has a different name: `findings.bak.txt`.
5. Start a simple long-running process in your home directory. Use a
   loop that appends the current time to a file once a second until you
   stop it:
   `while true; do date >> ~/lab02-notes/ticker.txt; sleep 1; done`.
6. While it's running, in a **second terminal**, confirm it's actually
   making progress by checking the line count twice, a couple of
   seconds apart: `wc -l ~/lab02-notes/ticker.txt` (expect the second
   number to be higher than the first).
7. Stop the process in the first terminal with `Ctrl+C`. Confirm in
   the second terminal that the line count has stopped growing: run
   `wc -l ~/lab02-notes/ticker.txt` twice more, a couple of seconds
   apart, and expect the same number both times.

## Acceptance criteria

- `~/lab02-notes/findings.bak.txt` and `~/lab02-notes/whoami-line.txt` both
  exist, and the latter contains exactly one line, matching `whoami`.
- You can point to the exact command that stopped the process, and explain
  what signal `Ctrl+C` sends.
- You can explain the difference between `>` and `>>`.

## Verification

```bash
test -f ~/lab02-notes/findings.bak.txt && echo "backup exists"
test -f ~/lab02-notes/whoami-line.txt && echo "grep output exists"
wc -l < ~/lab02-notes/whoami-line.txt   # expect exactly 1
find ~/lab01-notes ~/lab02-notes -name findings.txt | wc -l   # expect exactly 1
```

## Think about it

- What is the practical difference between piping (`|`) one command into
  another and redirecting (`>`) into a file?
- The process kept running after you pressed Enter on the first command.
  Why didn't your terminal give you a new prompt right away?
- What would `>` (instead of `>>`) have done to `findings.txt` if you had
  used it in Lab 01 by mistake?

## If you get stuck

- **Hint 1:** You need `cp`, `mv`, `rm` for file manipulation; `grep`,
  `find`, and `wc` for searching and counting; `>` and `>>` for
  redirection; `|` for piping.
- **Hint 2:** `grep "whoami" file > out.txt` writes matching lines from
  `file` into `out.txt`, overwriting it if it exists.
- **Hint 3:** To run a command and free up your terminal immediately, you
  can background it with `&`, but for this lab, use a second terminal tab
  or pane instead so you can watch both at once.

## What's next

You can now run commands and inspect their output. Next, you need a way
to see what changed in the project and to undo mistakes. That is what
version control is for.

Continue to [Lab 03 — You inherited a repository](../03-inherited-repository/README.md).
