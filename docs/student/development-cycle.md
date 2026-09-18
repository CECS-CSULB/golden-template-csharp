# Development Cycle

Use this cycle throughout the assignment. The goal is to make steady progress,
catch problems early, and finish with a repository that another developer could
understand and build.

## 1. Understand the requirements

Before writing code:

- Read [STUDENT_README.md](../../STUDENT_README.md), the primary source of the
  assignment requirements, and identify every required deliverable.
- Note required function names, signatures, files, input/output formats, and
  restrictions.
- Identify how the assignment will be tested and graded.
- Look for edge cases and examples in the instructions.
- Ask your instructor about anything that is ambiguous before relying on an
  assumption.

Write down a short checklist of requirements. Use it again during your final
review.

## 2. Make sure your tools work

Complete the [Git and GitHub command-line guide](github.md) if Git is not ready.
Then follow the [C# build guide](setup.md) to verify that you can:

- Restore the solution's NuGet dependencies.
- Build the program.
- Run the program with sample input.
- Run the test suite successfully.

Run the starter project before making changes. This confirms that your compiler,
dependencies, and build commands work independently of your solution.

## 3. Begin development

Study the starter code and tests before editing. Understand the existing data
flow, interfaces, and project layout. Then implement one small piece at a time.

Keep your changes focused. Avoid changing function signatures, public
interfaces, configuration files, or tests unless the assignment explicitly
allows it. Do not commit generated build files, executables, editor settings,
secrets, or unrelated changes.

## 4. Run tests occasionally

Run the test suite after each meaningful change or small group of changes. For
this project, the normal command is:

```bash
dotnet test --solution golden-template-csharp.sln --configuration Release
```

Use failing tests as feedback. Read the failure message, identify the smallest
likely cause, make one focused correction, and run the tests again. Add your
own local checks for important edge cases, but remember that local tests do not
replace the assignment's required tests.

If the test suite stops working because of a build or configuration problem,
return to the [C# build guide](setup.md) before debugging assignment logic.

## 5. Commit progress occasionally

Commit completed, understandable increments instead of waiting until the very
end. Before each commit:

```bash
git status
git diff
```

Then create a focused commit:

```bash
git add path/to/changed-file.cs
git commit -m "Implement median calculation"
```

Good commit messages briefly describe the change. Check that you are not
including build output, credentials, private course material, or accidental
edits to tests.

## 6. Complete the assignment

When the implementation is finished, compare it with your requirements
checklist. Confirm that:

- Every required feature is implemented.
- Required names, signatures, files, and output formats are unchanged.
- Normal cases and important edge cases work.
- The project builds from a clean configuration.
- The complete test suite passes locally.
- Required course documentation is complete.

## 7. Perform a final review

Review the changes as if you were submitting code for a professional project:

```bash
git status
git diff HEAD~1..HEAD
git log --oneline --decorate -n 5
```

Check for debugging output, dead code, unexplained workarounds, accidental
large files, secrets, and files that should not be public. Make sure the README
and build instructions are accurate enough for someone else to follow.

If your instructor provided special submission or file-integrity rules, verify
those rules now. Do not modify or remove tests simply to make the test suite
pass.

## 8. Push your work to GitHub

Use the [Git and GitHub command-line guide](github.md) if you need help with
authentication or remotes. Check the current branch and remote before pushing:

```bash
git branch --show-current
git remote -v
git push
```

If the assignment instructions require a particular branch, tag, or Classroom 50
submission command, follow those instructions instead of using a plain push.

## 9. Monitor the CI results

After pushing, open the repository on GitHub and check the **Actions** tab. Wait
for the CI workflow to finish and read the results:

- **Green:** the configured build and tests completed successfully.
- **Red:** open the failed job, read the log, fix the underlying problem, and
  push again.
- **Waiting or missing:** verify that the push reached the expected repository
  and branch, then check the workflow status and repository settings.

Local success does not guarantee CI success. CI may use a clean environment,
different paths, or freshly installed dependencies, so resolve CI failures
before considering the assignment complete.

## The cycle repeats

Development is iterative:

```text
Understand → Check tools → Implement → Test → Commit → Review → Push → Monitor CI
       ↑                                                                  |
       └────────────────────── fix and improve ───────────────────────────┘
```

You do not need to wait until the end to push, and you do not need to push every
experimental edit. Make local progress, commit sensible checkpoints, and push
often enough that your GitHub repository reflects your work and CI can provide
useful feedback.
