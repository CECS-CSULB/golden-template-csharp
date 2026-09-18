# Everyday Git Workflow

Use this guide after you have completed the initial setup in [Using Git and
GitHub from the Command Line](github.md). It covers the routine Git commands
you will use while working on the assignment.

Run these commands from the assignment directory—the folder that contains
`golden-template-csharp.sln` and `.git`.

## Check the repository before you start

Begin a work session by checking whether your local copy has changes:

```bash
git status
```

The output tells you which files are modified, added, deleted, or not yet
tracked. A clean working tree includes a message similar to:

```text
nothing to commit, working tree clean
```

If you have local changes, review them before doing anything that might update
the repository:

```bash
git diff
```

Do not ignore a change just because you do not remember making it. Ask your
instructor for help if you cannot identify an important file.

## Get instructor-provided updates

Only pull updates when your instructor says that the assignment repository has
changed and students should receive those changes. First make sure your local
work is saved in a commit:

```bash
git status
git add path/to/your-completed-change.cs
git commit -m "Save progress before updating"
```

Then download and apply updates from the repository:

```bash
git pull --ff-only
```

The `--ff-only` option prevents Git from creating an unexpected merge commit.
If Git refuses to pull because your local copy has uncommitted changes, stop
and save those changes in a commit before trying again. If the pull reports a
problem that you do not understand, do not delete files or reset the repository;
ask your instructor for help.

You usually do not need to pull before every work session. Pull when the course
instructions announce a starter-code, test, or documentation update.

## Review and stage your changes

After making a small, understandable change, inspect it:

```bash
git status
git diff
```

Stage only the files that belong in the commit. Naming files explicitly gives
you more control than staging everything:

```bash
git add src/Library/Statistics.cs tests/StatisticsTests/StatisticsTests.cs
```

Review the staged version before committing:

```bash
git diff --cached
```

If the staged diff contains an accidental file, unstage it without deleting
your work:

```bash
git restore --staged path/to/accidental-file
```

Run the local build and tests after staging or before committing, as required
by the [development cycle](development-cycle.md).

## Commit a focused checkpoint

Create a commit when the staged changes form a complete, understandable
increment:

```bash
git commit -m "Implement median calculation"
```

Use a short message that describes what changed. Then confirm that the commit
was created and that no unintended files remain:

```bash
git status
git log --oneline -n 3
```

It is normal to make several commits during an assignment. A commit is a local
checkpoint; it does not send anything to GitHub until you push it.

## Push committed work to GitHub

When you are ready to save your checkpoint remotely:

```bash
git push
```

If Git says that no upstream is configured, use the first-push command from
[the GitHub guide](github.md), or follow the repository-specific command your
instructor provided. After the push finishes, open the repository on GitHub and
check that the new commit appears. The push should also start the repository's
GitHub Actions workflow when CI is configured.

Push regularly enough that your GitHub repository reflects meaningful progress,
but do not push code that contains secrets, passwords, tokens, generated build
files, or unfinished experiments that you do not want recorded remotely.

## Correct common local mistakes

### I staged a file by accident

Unstage it while keeping the file and its edits:

```bash
git restore --staged path/to/file
```

### I changed a file but want to discard those edits

First inspect the file and confirm that you do not need the changes:

```bash
git diff -- path/to/file
```

Then restore the file to its last committed state:

```bash
git restore path/to/file
```

This discards uncommitted edits to that file. If you are unsure, do not run
`git restore`; make a backup copy or ask for help first.

### I started a commit but Git reported an error

A commit that reports an error usually was not created. Check the status and
fix the reported problem:

```bash
git status
git diff --cached
```

For example, configure your Git identity if Git says it does not know who you
are:

```bash
git config --global user.name "Your Full Name"
git config --global user.email "you@example.com"
```

After correcting the problem, run the commit command again.

### I committed the wrong files, but have not pushed yet

Do not immediately create a second corrective commit if the mistake is only in
your most recent local commit. You can undo that commit while keeping the files
as local changes:

```bash
git reset --soft HEAD~1
```

Review `git status` and `git diff --cached`, remove anything that should not be
included, and create the corrected commit. Use this only for a commit that has
not been pushed to GitHub. If the commit has already been pushed, leave the
history intact and create a new corrective commit instead.

### I committed a secret or token

Stop pushing immediately. Revoke the credential, tell your instructor, and
follow GitHub's guidance for
[removing sensitive data from a repository](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository).
Deleting the file in a later commit does not remove the secret from the earlier
commit history.

## A compact routine

For a normal work session, this is usually enough:

```bash
git status
git diff
# edit files
dotnet build golden-template-csharp.sln --configuration Release
dotnet test --solution golden-template-csharp.sln --configuration Release --no-build
git add path/to/changed-file.cs
git diff --cached
git commit -m "Describe the completed change"
git push
```

If a command produces an unfamiliar result, save the complete command and
error message before asking for help. Do not include passwords, access tokens,
or other secrets in a help request.
