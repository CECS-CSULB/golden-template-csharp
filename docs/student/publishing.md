# Publish Your Completed Assignment

This guide explains how to publish a completed classroom assignment as a
public repository on your personal GitHub account.

> [!CAUTION]
> Do this **only after the semester has ended and your instructor has given
> permission** to publish this assignment. A Classroom 50 assignment normally
> lives in a private repository owned by your classroom organization. Making that public
> without permission could open you to academic integrity problems.

The safe workflow is:

1. Review the project with your instructor.
2. Create a new, empty public repository under your personal GitHub account.
3. Keep the classroom repository as your existing "origin" remote.
4. Add the new public repository as a second remote.
5. Push only the approved branch to the new remote.

## Before publishing

Ask your instructor to confirm all of the following:

- The assignment may be published publicly.
- The current branch and commit history are approved for publication.
- Public tests, starter code, and course-specific files may be shared.
- No solution, rubric, hidden test, student data, or instructor-only material is
  included.
- The project may be licensed or reused as shown in the new repository.

Public means that anyone on the internet can read, copy, and download the
repository. This cannot be undone merely by making the repository private later;
someone may already have copied it.

### Check the files in your local clone

From the assignment directory, review the working tree and recent history:

~~~bash
git status
git branch --show-current
git log --oneline --decorate -n 10
git ls-files
~~~

Look especially for:

- Access tokens, passwords, API keys, private keys, or other secrets.
- Personal information belonging to you or another student.
- Instructor solutions, grading scripts, hidden tests, or answer keys.
- Course configuration files that your instructor says must remain private.
- Classroom 50 control files such as `.classroom50.yaml` and
  `.github/workflows/autograde.yaml`, unless your instructor explicitly says
  they may be published.
- Generated build directories, executables, editor settings, and temporary files.

Deleting a secret from the latest version does not remove it from earlier Git
commits. If a secret was ever committed, stop and tell your instructor; revoke
or rotate it before doing anything else.

The same history rule applies to private course files. The push command below
publishes the history reachable from your approved branch, so ask your
instructor whether a clean portfolio history is required before pushing.

## Install and configure Git

Complete [Using Git and GitHub from the Command Line](github.md) first. It
covers installing Git on Windows and macOS, configuring your Git identity,
authenticating with GitHub, and securely storing your credentials.

Return here after `git --version` works and Git is configured.

## Create the public repository

Create a new repository under your **personal account**, not under the
classroom organization:

1. Open [github.com/new](https://github.com/new).
2. In **Owner**, select your personal account.
3. Choose a professional repository name, for example
   data-structures-tree-assignment.
4. Add a short description explaining what the project demonstrates.
5. Select **Public**.
6. Leave **Add a README file**, **Add .gitignore**, and **Choose a license**
   unchecked for now. Your local repository already has its own history and
   files; initializing the GitHub repository would create an unrelated first
   commit.
7. Click **Create repository**.

## Add the public repository as a second remote

In your existing local assignment clone, first inspect the current remote:

~~~bash
git remote -v
~~~

You should see the private classroom repository listed as `origin`. Do not replace
it. Add the public repository with the name `portfolio`:

~~~bash
git remote add portfolio https://github.com/YOUR-USERNAME/YOUR-PUBLIC-REPOSITORY.git
~~~

Replace YOUR-USERNAME and YOUR-PUBLIC-REPOSITORY with your values. Confirm both
remotes:

~~~bash
git remote -v
~~~

The remote names are local configuration; they are not published to GitHub.

## Push the approved branch

Before pushing, check the current branch and make sure the working tree is in
the state your instructor approved:

~~~bash
git status
git branch --show-current
git log --oneline --decorate -n 5
~~~

The following command previews the push without changing the public repository:

~~~bash
git push --dry-run portfolio HEAD:main
~~~

If the preview looks correct, push the current branch to the public repository's
main branch:

~~~bash
git push portfolio HEAD:main
~~~

## Verify the public repository

Open the new repository in a private browser window or while signed out. Check
that:

- The repository is public and owned by your personal account.
- The README explains the project clearly.
- The source code and tests that your instructor approved are present.
- No secrets, private course information, or other students' information is
  visible.
- The build and test instructions work for someone who did not take the class.
- GitHub Actions, if included, do not expose private course configuration.

You can also verify the public remote locally:

~~~bash
git ls-remote portfolio
~~~

## Publishing future improvements

Keep origin and portfolio distinct:

~~~bash
git remote -v
~~~

Use the explicit remote name when publishing an update:

~~~bash
git add path/to/approved-file
git commit -m "Improve project documentation"
git push portfolio HEAD:main
~~~

Review git status and the destination before every push. A plain git push may
use whichever upstream branch is configured locally, so do not rely on it until
you understand where it will send changes.

## Troubleshooting

### remote portfolio already exists

Inspect the existing URL:

~~~bash
git remote get-url portfolio
~~~

If it is the correct public repository, keep using it. If it is wrong, ask your
instructor before changing it, then update it with:

~~~bash
git remote set-url portfolio https://github.com/YOUR-USERNAME/YOUR-PUBLIC-REPOSITORY.git
~~~

### The push is rejected because the remote contains work

This usually means the GitHub repository was initialized with a README,
.gitignore, or license. Do not force-push without instructor permission. Stop,
show the error to your instructor, and decide whether to recreate the empty
repository or merge the unrelated commit safely.


### I accidentally exposed a token

Immediately revoke the token in GitHub **Settings → Developer settings →
Personal access tokens** and create a replacement if necessary. Tell your
instructor. Removing the token from the latest commit is not enough if it was
already pushed or appears in earlier history.

## Official references

- [Installing Git for Windows](https://git-scm.com/install/windows.html)
- [Installing Git for macOS](https://git-scm.com/install/mac)
- [Creating a repository on GitHub](https://docs.github.com/en/get-started/start-your-journey/creating-a-repository-for-your-project-on-github)
- [Managing personal access tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
- [Caching GitHub credentials in Git](https://docs.github.com/en/get-started/git-basics/caching-your-github-credentials-in-git)
- [Removing sensitive data from a repository](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
