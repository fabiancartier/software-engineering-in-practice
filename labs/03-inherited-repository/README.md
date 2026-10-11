# Lab 03 — You inherited a repository

## Story

You now have access to this repository. Before changing anything, you
need to inspect its state and learn how to record your own work safely.

## Learning objectives

After this lab you should be able to:

- Explain the difference between the working directory, the staging area,
  and the local repository.
- Inspect the current state of a repository with `git status`, `git log`,
  and `git diff`.
- Stage and commit a change with a clear message.

## Before you start

- Lab 02 complete.
- You are inside your own clone of this repository (you set this up in
  [`START-HERE.md`](../../START-HERE.md); if you're using
  Codespaces instead, it's already provided for you).
- Your current directory is the repository root.

## Your task

1. Run `git status` and `git log` in the repository root. Read the output
   before doing anything else.
2. Create a new file `labs/03-inherited-repository/notes/my-observations.txt`
   containing at least two sentences. Describe what `git status` showed
   you in one and what `git log` showed you in the other.
3. Run `git status` again. Add a third line to the same file explaining
   in your own words why the new file appears as it does.
4. Stage only that file with `git add`.
5. Run `git diff --staged` and observe what it shows compared to plain
   `git diff`.
6. Commit the staged change with a clear, English, present-tense message,
   for example `docs: add lab 03 observations`. If this is the first commit
   you've ever made on this machine, Git may stop you with "Please tell
   me who you are" instead of committing. It needs a name and email for
   every commit you make. Run these two commands once
   (use the email tied to your GitHub account), then repeat the commit:
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com"
   ```
7. Run `git log` once more and confirm your commit appears at the top.

## Acceptance criteria

- `labs/03-inherited-repository/notes/my-observations.txt` exists, is
  committed, and contains at least three lines as described above.
- `git log` shows your commit with a clear message, in English.
- You can explain, without re-reading Git docs, what "staged" means.

## Verification

```bash
git log --oneline -1                      # your commit should be at HEAD
git status                                 # should be clean (nothing to commit)
test -f labs/03-inherited-repository/notes/my-observations.txt && echo "notes exist"
wc -l < labs/03-inherited-repository/notes/my-observations.txt  # expect >= 3
```

## Think about it

- `git diff` and `git diff --staged` showed different things. Why does Git
  distinguish between these two states at all?
- If you had run `git commit` without `git add` first, what would have
  happened to your new file?

## If you get stuck

- **Hint 1:** You need exactly five Git commands here: `status`, `log`,
  `diff`, `add`, `commit`.
- **Hint 2:** `git diff` (no arguments) shows unstaged changes; `git diff
  --staged` shows what will actually go into the next commit.
- **Hint 3:** A commit needs a message. Use `git commit -m "your message
  here"` rather than opening an editor, unless you're comfortable with one.
- **Hint 4:** "Please tell me who you are" means Git has no `user.name`
  or `user.email` configured yet on this machine. Step 6 contains the
  two `git config --global` commands that complete this setup.

## What's next

Your commit exists only on this machine, in the local repository. Nobody
else can see it yet. Next you will learn what a remote repository is.

Continue to [Lab 04 — Local is not remote](../04-local-vs-remote/README.md).
