# Initial Git and GitHub Setup from the Command Line

This guide explains how to install Git, configure it, authenticate with GitHub,
and connect your local repository from a terminal. It assumes that you already
have a GitHub account.

The preferred setup uses an HTTPS connection and Git Credential Manager (GCM).
GCM opens a browser sign-in and securely remembers your GitHub credentials, so
you can use Git with every repository you are allowed to access. Complete this
guide once. For routine status checks, commits, pulls, and pushes, use the
[Everyday Git Workflow](git-workflow.md).

## 1. Install Git

Choose the section for your operating system.

### Windows

Install the latest version of [Git for Windows](https://git-scm.com/install/windows.html).
You can use the installer, or open PowerShell and run:

```powershell
winget install --id Git.Git -e --source winget
```

Accept the installer's default choices unless you have a reason to change them.
Git for Windows includes Git Credential Manager, which can securely remember
your GitHub credentials.

Close and reopen PowerShell after installation, then verify Git:

```powershell
git --version
```

If Git is installed, you should see output similar to `git version 2.55.0`.
If PowerShell says that `git` is not recognized, close and reopen PowerShell
and try again. If it still fails, rerun the installer and make sure Git is
installed for use from the command line.

### macOS

The simplest option is Apple's Command Line Tools. Open Terminal and run:

```bash
xcode-select --install
```

Complete the installation dialog. Alternatively, if you use Homebrew, install
Git with:

```bash
brew install git
```

Verify Git:

```bash
git --version
```

If Git is installed, you should see output similar to `git version 2.55.0`.
If Terminal says `git: command not found`, finish the Command Line Tools
installation, open a new Terminal window, and try again. If you installed Git
with Homebrew, run `brew install git` and repeat the check.

## 2. Configure your Git identity

Git records a name and email address in each commit. These settings are not
your GitHub login credentials.

Replace the example values with your own information:

```bash
git config --global user.name "Your Full Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
```

Use an email address verified on GitHub if you want commits to be associated
with your GitHub account. You can use your GitHub-provided private `noreply`
address if you do not want to publish your personal email address. Find it in
[GitHub Settings → Emails](https://github.com/settings/emails).

Check the settings:

```bash
git config --global --list
```

## 3. Preferred: authenticate with Git Credential Manager

GitHub recommends using a credential helper rather than repeatedly typing or
storing credentials in a plain-text file. GCM uses your browser for sign-in and
stores the resulting credential in your operating system's secure credential
store.

### Windows

Git for Windows includes Git Credential Manager. It is normally configured
automatically. If it is not already configured, run this in PowerShell:

```powershell
git config --global credential.helper manager
```

The first time you clone or push over HTTPS, GCM should open a browser window.
Sign in to GitHub there and approve the request. You do not need to create or
paste a PAT when this browser flow works.

### macOS

Install Git Credential Manager with Homebrew:

```bash
brew install --cask git-credential-manager
```

GCM normally configures Git automatically. If it is not already configured, run:

```bash
git config --global credential.helper manager
```

The first time you clone or push over HTTPS, GCM should open a browser window.
Sign in to GitHub there and approve the request. If you cannot use GCM, the
macOS Keychain is a secure fallback:

```bash
git config --global credential.helper osxkeychain
```

Check which helper Git is using:

```bash
git config --global --get credential.helper
```

The output should be `manager` or `manager-core` for GCM, or `osxkeychain` when
using the macOS Keychain fallback.

## 4. Optional fallback: create a GitHub access token

Most students should skip this section. Use a PAT only if GCM or browser-based
authentication is unavailable, or if your instructor specifically asks you to
use one. One PAT can cover multiple assignment repositories; you do not need a
separate PAT for each assignment.

GitHub no longer accepts your normal account password for Git operations over
HTTPS. Create a fine-grained PAT to use as the password when Git prompts you.

1. Open GitHub's [new fine-grained token page](https://github.com/settings/personal-access-tokens/new).
2. Enter a descriptive **Token name**, such as `course-git-fall-2026`.
3. Set an **Expiration**. Choose a date that covers the time you need the
   token, such as the end of the semester.
4. Select the **Resource owner** that owns the repositories you will push to.
   This is usually your personal account. If the assignment repositories belong
   to a course organization, select that organization instead.
5. Under **Repository access**, choose **Only select repositories**, then select
   the assignment repositories you need. Choose **All repositories** only if your
   instructor explicitly tells you to and you understand the wider access.
6. Under **Repository permissions**, set **Contents** to **Read and write**.
   Leave unrelated permissions off. Git operations normally do not need
   administrator, Actions, Issues, or pull-request permissions.
7. Click **Generate token** and copy the token immediately. GitHub will not show
   the complete token again.

If an organization does not appear as a resource owner, or the token shows
**Pending** approval, ask your instructor or organization administrator. The
organization may require approval for fine-grained tokens.

## 5. Use GitHub credentials with an HTTPS repository

Make sure the repository uses an HTTPS URL. It should look like this:

```text
https://github.com/OWNER/REPOSITORY.git
```

Do not put the token in that URL.

### Clone this repository

Use the HTTPS URL for the C# assignment repository supplied by your instructor. For this template, the commands are:

```bash
git clone https://github.com/csulb-cecs-demo/cecs-golden-template-csharp.git
cd cecs-golden-template-csharp
```

With GCM configured, Git should open a browser window. Sign in to GitHub and
approve the request. Your credential helper will remember the result for later
operations.

If GCM is unavailable and you created the optional PAT, Git may instead ask for
credentials. Enter:

```text
Username: your-github-username
Password: paste-your-personal-access-token-here
```

The terminal may not display anything while you paste the token. That is
normal. Press **Enter** after pasting it. The credential helper should remember
it securely for later pushes.

### Connect an existing local repository

From inside the repository directory, inspect the current remote:

```bash
git remote -v
```

If the remote is missing or uses an SSH URL, set it to the HTTPS URL for your
repository:

```bash
git remote set-url origin https://github.com/OWNER/REPOSITORY.git
```

If there is no `origin` remote yet, use:

```bash
git remote add origin https://github.com/OWNER/REPOSITORY.git
```

## 6. Confirm the setup

From the repository directory, confirm that Git can see the remote:

```bash
git remote -v
```

You can test read access without changing anything on GitHub:

```bash
git ls-remote origin
```

If the command lists references, authentication and read access are working.
You do not need to create a commit or push anything just to test your
credentials.

The initial setup is complete. Use the
[Everyday Git Workflow](git-workflow.md) for:

- Checking status and reviewing changes.
- Pulling instructor-provided updates.
- Staging files and creating commits.
- Pushing committed work to GitHub.
- Correcting common local mistakes.

## Troubleshooting

### `Authentication failed`

Check the following:

- The remote URL is HTTPS, not an SSH URL:

  ```bash
  git remote -v
  ```

- The token has not expired.
- The token's resource owner is the owner of the repository.
- The repository is included under **Only select repositories**.
- The token has **Contents: Read and write** permission.
- An organization administrator has approved the token, if approval is
  required.

If an old or incorrect credential was saved, remove the GitHub credential and
try again. On Windows, open **Credential Manager → Windows Credentials**, find
the GitHub entry, and remove it. On macOS, open **Keychain Access**, search for
`github.com`, and delete the saved GitHub internet-password entry. The next Git
operation will ask for credentials again.

### `Permission denied` or `403`

Authentication succeeded, but your GitHub account does not have write access to
that repository. Confirm that you are pushing to your own assignment repository
and that you accepted the assignment or joined the course organization as
required.

### I accidentally exposed my token

Immediately revoke it in [GitHub's fine-grained token settings](https://github.com/settings/personal-access-tokens),
remove the exposed credential from your computer, and create a new token. If it
was committed to a repository, tell your instructor and follow
GitHub's [guidance for removing sensitive data](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository).

## Further reading

- [GitHub: Managing personal access tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
- [GitHub: Caching credentials in Git](https://docs.github.com/en/get-started/git-basics/caching-your-github-credentials-in-git)
- [Git: First-time setup](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)
