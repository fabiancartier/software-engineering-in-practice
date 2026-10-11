# Your first push to GitHub

[Po polsku →](github-auth.pl.md) · [← Back to Start here](../../START-HERE.md)

Cloning and reading the repository does not require a login. The first
`git push` does require you to authenticate with GitHub. Return to this
page when a lab first asks you to push changes.

## What NOT to do

- **Never type your GitHub account password** into the terminal. GitHub
  does not accept account passwords for Git operations over HTTPS. If
  you are asked for one, see [If you get stuck](#if-you-get-stuck).
- **Never paste a token** (anything starting with `ghp_` or similar)
  into this repository's README, into a GitHub issue, into a commit
  message, or into a message to your instructor. Revoke any token that
  has been shared with someone else (**Settings → Developer settings →
  Personal access tokens**) and generate a new one.

## What to do instead

### The simplest path: sign in through the browser

When you run `git push` for the first time, VS Code or Git will usually
open a GitHub sign-in page in your browser. Sign in, complete two-factor
authentication if enabled, and click **Authorize**. Then return to the
terminal. The push should continue automatically.

**Git Credential Manager** handles this process. It is bundled with Git
for Windows and is often available with Git on macOS and Linux. It
stores the result of the sign-in, so you will usually do this only once
per computer.

### If no browser window appears

1. Try the push again:
   ```bash
   git push
   ```
2. If Git prints a prompt like `Username for
   'https://github.com':`, type your GitHub username and press Enter.
3. If it then prints `Password for
   'https://<username>@github.com':`, do **not** enter your account
   password. Enter a **personal access token**, a separate credential
   that you can revoke later. To create one:
   - In your browser, go to **GitHub → Settings → Developer settings →
     Personal access tokens → Tokens (classic) → Generate new token**.
   - Give it a name you'll recognize (e.g. `software-engineering-in-practice`),
     an expiration (30-90 days is plenty for a course), and check the
     **repo** scope.
   - Click **Generate token** and copy it immediately. GitHub will not
     show it again.
4. Paste the token into the terminal's password prompt and press Enter.
   The terminal will not display characters while you paste.

A successful push ends with output similar to `main -> main` and no
error.

## If you get stuck

- **"It's asking me for a password and token didn't work either."**
  Double-check you copied the entire token with no extra spaces, and
  that it hasn't expired. Generate a fresh one if unsure.
- **"I don't see a browser window or a prompt in the terminal."**
  Press `Ctrl+C` to cancel, then try
  `git push` again from a freshly opened terminal.
- **Still stuck?** See the shared
  [`troubleshooting.md`](troubleshooting.md) for how to report the
  problem. Never include a password, token, or other secret in the
  report.
