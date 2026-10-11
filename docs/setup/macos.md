# macOS setup

[Po polsku →](macos.pl.md) · [← Back to Start here](../../START-HERE.md)

If you get stuck anywhere, jump to [If you get stuck](#if-you-get-stuck)
below, or the shared [`troubleshooting.md`](troubleshooting.md).

## Part 1: Open Terminal

1. Press `Cmd+Space` to open Spotlight search.
2. Type `Terminal`.
3. Press Enter, or click **Terminal** when it appears.

**What you should see:** a window with a text prompt, something like
`yourname@Yourname-MacBook ~ %`.

**What to do if you see something else:** if Spotlight doesn't find
Terminal, it's also in **Applications → Utilities → Terminal** via
Finder.

## Part 2: Check for Xcode Command Line Tools

Git on macOS is bundled with Apple's Command Line Tools, not installed
on its own.

1. In Terminal:
   ```bash
   git --version
   ```
2. **There are two possible results:**
   - A version number such as `git version 2.39.3 (Apple Git-145)`.
     The tools are already installed, so continue to Part 3.
   - A popup window: "The 'git' command requires the command line
     developer tools. Would you like to install the tools now?". Click
     **Install**, then accept the license agreement that follows.
3. If you saw the popup, wait for the download and install to finish
   (this can take several minutes depending on your connection), then
   confirm:
   ```bash
   git --version
   ```
   **How you know you can move on:** a version number prints.

## Part 3: Install VS Code

The simplest path installs via Homebrew, macOS's command-line package
manager. If you'd rather not install Homebrew, use the direct-download
alternative below instead.

### Option A: Homebrew (recommended)

1. Check whether Homebrew is already installed:
   ```bash
   brew --version
   ```
2. **If you see** `command not found: brew`, install it with the
   official command from [brew.sh](https://brew.sh). Paste it without
   changes:
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```
3. Follow the instructions printed at the end. On Apple
   Silicon Macs, Homebrew's installer typically asks you to run one or
   two more commands to add it to your shell's `PATH`. Copy and run
   the command it shows you.
4. Close Terminal completely and reopen it (Part 1), then confirm:
   ```bash
   brew --version
   ```
   **How you know you can move on:** a version number prints.
5. Install VS Code:
   ```bash
   brew install --cask visual-studio-code
   ```
6. **What you should see:** download and install output ending without
   an error.

### Option B: Direct download

1. Go to [code.visualstudio.com](https://code.visualstudio.com/) in
   your browser and download the macOS build.
2. Open the downloaded `.zip`. It expands to `Visual Studio Code.app`.
3. Drag it into your **Applications** folder.

### Launch VS Code

Press `Cmd+Space`, type `Visual Studio Code`, press Enter.

**What you should see:** a VS Code window opens with a welcome tab and
an empty Explorer panel on the left. The panel is empty because no
project is open yet.

## Part 4: Open a terminal inside VS Code

1. In the VS Code menu, click **Terminal → New Terminal**.
2. **What you should see:** a terminal panel opens at the bottom of the
   window, with a prompt similar to the one from Part 1. VS Code uses
   zsh by default, so no extra configuration is required.
3. Confirm:
   ```bash
   pwd
   ```
   **What you should see:** a path, probably ending in your home
   directory (something like `/Users/yourname`).

## Part 5: Current directory and home directory

- `pwd` prints the current directory.
- `ls` lists what's in the current directory.
- `cd` with no arguments, or `cd ~`, always takes you back to your home
  directory, no matter how lost you feel.
- `cd ..` moves up one directory level.

If a command's output looks unfamiliar, run `pwd` first. Many problems
come from working in a different directory than expected.

## Part 6: Fork, clone, and open the repository

This is the actual point of this page. From here on, everything happens
either in your browser or in the VS Code terminal from Part 4.

**Already have a fork or a local copy from an earlier attempt?** Do not
repeat these steps. Go to
[`already-have-a-fork.md`](already-have-a-fork.md) first.

### 6.1: Fork the repository on GitHub

1. Make sure you're logged into GitHub. Open
   [github.com](https://github.com) in your browser and check that the
   top-right corner shows your account avatar, not a "Sign in" button.
   If you don't have a GitHub account yet, create one (it's free)
   before continuing.
2. Go to
   `https://github.com/michalmaj/software-engineering-in-practice`.
3. Click the **Fork** button, top right of the page.
4. On the "Create a new fork" page, make sure **Owner** shows *your*
   account, not the instructor's.
5. Click **Create fork**.

**What you should see:** GitHub takes you to a new page whose URL is
`https://github.com/<your-username>/software-engineering-in-practice`.
Your username, not `michalmaj`, should appear in the address bar and
page title. GitHub also shows that the repository is a fork of
`michalmaj/software-engineering-in-practice`.

**How you know you can move on:** the browser address bar contains
*your* GitHub username, not `michalmaj`.

### 6.2: Copy your fork's clone address

1. On your fork's page (from the previous step), click the green
   **Code** button.
2. Make sure the **HTTPS** tab is selected, not SSH or GitHub CLI. This
   course does not require SSH keys.
3. Click the small copy icon next to the address to copy it. It looks
   like:
   ```text
   https://github.com/<your-username>/software-engineering-in-practice.git
   ```
   with your username in place of `<your-username>`.

### 6.3: Clone it onto your computer

Back in the VS Code terminal from Part 4:

1. Decide where your projects should live, and create that folder if it
   doesn't already exist. A reasonable, simple choice:
   ```bash
   mkdir -p ~/projects
   cd ~/projects
   ```
2. Clone your fork using the address from step 6.2. **Replace
   `<your-username>` with your GitHub username**; do not paste the
   line below literally:
   ```bash
   git clone https://github.com/<your-username>/software-engineering-in-practice.git
   ```
3. **What you should see:** lines like `Cloning into
   'software-engineering-in-practice'...`, then `Receiving objects:
   100%`, ending without an error.
4. Move into the new folder:
   ```bash
   cd software-engineering-in-practice
   ```
5. Confirm where you are and what's there:
   ```bash
   pwd
   ls
   ```
   **What you should see:** `pwd` prints something ending in
   `/projects/software-engineering-in-practice`; `ls` lists folders
   including `labs`, `examples`, `scripts`, and files including
   `README.md` and `START-HERE.md`.
6. Confirm Git itself is happy, and that it points at *your* fork, not
   the instructor's:
   ```bash
   git status
   git remote -v
   ```
   **What you should see:** `git status` says `On branch main` and
   `nothing to commit, working tree clean`. `git remote -v` shows two
   lines (`origin` fetch and push) whose address contains **your own
   GitHub username**. If you see `michalmaj`, you cloned the original
   repository instead of your fork. See
   [I cloned the instructor's repository instead of my own fork](#i-cloned-the-instructors-repository-instead-of-my-own-fork)
   below.

**How you know you can move on:** `git remote -v` shows your own
username, and `ls` shows `labs/`, `examples/`, and `scripts/`.

### 6.4: Open the repository in VS Code

1. In VS Code, click **File → Open Folder…** (or **File → Open…** in
   some versions; it still opens a folder picker on macOS).
2. Navigate to the cloned directory. If you used the location from
   step 6.3, choose `projects` → `software-engineering-in-practice`
   inside your home folder.
3. Click **Open**.
4. VS Code may ask "Do you trust the authors of the files in this
   folder?" Click **Yes, I trust the authors**.

**What you should see:** the Explorer panel on the left now lists
`labs`, `examples`, `scripts`, `README.md`, and the other entries shown
earlier by `ls`.

5. Open a terminal inside this window (**Terminal → New Terminal**).
   Confirm you're in the right place:
   ```bash
   pwd
   ```
   **How you know you can move on:** the path ends in
   `/software-engineering-in-practice`, and the Explorer panel shows a
   `labs/` folder.

### 6.5: Find Lab 01

1. In the Explorer panel on the left, click to expand the `labs`
   folder.
2. Find and click `01-workstation`, then click `README.md` inside it.

**What you should see:** `Lab 01 — Meet your workstation` opens as
readable text in the editor.

The setup is ready. Go to [Before you're done](#before-youre-done),
check every item, and start the lab.

## Your first push to GitHub

Your first push comes in Lab 03 or Lab 04. Cloning and reading the
repository do not require Git authentication. When `git push` asks you
to sign in, use [`github-auth.md`](github-auth.md).

## Before you're done

Check every one of these before you consider Lab 01 ready to start:

- [ ] `git --version` works in Terminal.
- [ ] VS Code's terminal opens and behaves like the terminal from Part
      1.
- [ ] `pwd` inside the VS Code terminal ends in
      `/software-engineering-in-practice`.
- [ ] `git status` works without an error.
- [ ] `git remote -v` shows **your own** GitHub username.
- [ ] The Explorer panel shows `labs/`, `examples/`, and `scripts/`.
- [ ] `labs/01-workstation/README.md` opens.

Everything checked? Open
[`labs/01-workstation/README.md`](../../labs/01-workstation/README.md)
and begin.

## If you get stuck

### `brew`, `git`, or `code` says "command not found"

Close Terminal and open it again as described in Part 1. A newly
installed program and Homebrew's `PATH` configuration may not work in a
window that was open before the installation finished. If `brew` is
still missing, check the end of the Homebrew installation output from
Part 3 for an additional `PATH` command. That step is easy to miss.

### I cloned the instructor's repository instead of my own fork

If `git remote -v` shows `michalmaj` instead of your username: the
folder you cloned points at a repository you can't push to, which will
block you later (Lab 03–04). Fix it without losing anything:

```bash
cd ..
mv software-engineering-in-practice software-engineering-in-practice.instructor-copy
```

This renames the wrongly-cloned folder out of the way instead of
deleting it, then repeat 6.1–6.4 with *your* fork's address this time.
Once the correct copy works, you can delete the renamed directory or
leave it on disk.

### Everything else

See the shared [`troubleshooting.md`](troubleshooting.md) for issues
that aren't specific to macOS (GitHub login problems, "the repo is
already on disk", VS Code opened the wrong folder, first-push problems,
and more).
