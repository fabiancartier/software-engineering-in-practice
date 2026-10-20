# Already have a fork or a local copy?

[Po polsku →](already-have-a-fork.pl.md) · [← Back to Start here](../../START-HERE.md)

Use this guide if you already have a fork or local copy but do not know
where to continue. The steps below do not delete or overwrite
anything. Stop and ask for help if continuing would require either
operation.

## "I forked it on GitHub, but I don't know if I cloned it"

1. Open [github.com](https://github.com) in your browser, click your
   avatar (top right), click **Your repositories**.
2. Look for `software-engineering-in-practice` in the list. If it is
   there, the fork already exists. Do not create another one.
3. Check whether the repository is also on your computer. Open a
   terminal and run:
   ```bash
   find ~ -maxdepth 4 -iname "software-engineering-in-practice" -type d 2>/dev/null
   ```
   If this prints a path, continue with "I have a local copy already"
   below. If it prints nothing, use the "Clone your fork" step in your
   system's guide: [Windows](windows.md#63-clone-the-repository-onto-your-computer),
   [macOS](macos.md#63-clone-it-onto-your-computer),
   [Linux](linux.md#63-clone-it-onto-your-computer).

## "I have a local copy already"

1. Open a terminal (or the one inside VS Code) and go to the folder the
   `find` command above printed, for example:
   ```bash
   cd ~/projects/software-engineering-in-practice
   ```
2. Check that the repository points to your fork:
   ```bash
   git remote -v
   ```
   **If this shows your GitHub username**, continue with "Opening it
   again" below.

   **If this shows `michalmaj` instead of your username**, you have a
   clone of the instructor's repository. Do not delete it. Rename the
   directory, then clone your fork:
   ```bash
   cd ..
   mv software-engineering-in-practice software-engineering-in-practice.instructor-copy
   ```
   Then follow the "Clone your fork" step in your system's guide.

3. Check the state of the local files:
   ```bash
   git status
   ```
   - `nothing to commit, working tree clean` means it is safe to
     continue.
   - Any other output may indicate modified or untracked files.
     **Do not discard them.** Leave them in place and ask your
     instructor if you do not know where they came from. Never run a
     command that discards changes (see
     [What never to do here](#what-never-to-do-here) below) just to
     make the message go away.

## Opening it again

1. Open VS Code.
2. **File → Open Folder…**, navigate to the folder from the step above.
3. Open a terminal inside VS Code (**Terminal → New Terminal**) and
   confirm:
   ```bash
   pwd
   git status
   ```
   **How you know you're in the right place:** `pwd` ends in
   `/software-engineering-in-practice`, and `git status` runs without
   an error.

## "My fork looks out of date compared to the course repository"

Your fork may fall behind when the instructor updates the course
repository. You do not need to sync it before Lab 01. Wait until a lab
asks you to do so, then use GitHub's sync feature:

1. On your fork's page on GitHub, look for a **Sync fork** button
   (GitHub shows this automatically when your fork is behind).
2. Click it, then **Update branch**.
3. Back in your terminal, update your local copy to match:
   ```bash
   git pull
   ```

If `git pull` reports a conflict, stop and ask your instructor. Do not
try to remove it with any of the commands listed below.

## What never to do here

Do not use these commands to fix a setup problem. Each can permanently
delete work that has not been pushed elsewhere:

- `git reset --hard`
- `git clean -fd` (or `-fdx`)
- `rm -rf` on your repository folder
- `git push --force` or `git push -f`

If you think you need one of them, stop and describe the problem to
your instructor. See [`troubleshooting.md`](troubleshooting.md) for
what information to include.

## Still not sure what state you're in?

See [`troubleshooting.md`](troubleshooting.md), or return to
[Start here](../../START-HERE.md) and follow your system's guide from
the beginning. Do not guess if a fix could put your work at risk.
