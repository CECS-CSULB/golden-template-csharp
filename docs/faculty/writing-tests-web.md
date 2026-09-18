# Writing Classroom 50 tests in the Web UI

The template contains the xUnit test files, but Classroom 50 also needs a
grading instruction that runs them. Configure that instruction in the Web UI.

## Add the C# test

Open your assignment, choose **Tests**, and click **Add test → Run command**.
Enter:

| Field | Value |
|---|---|
| **Test name** | `xUnit suite` |
| **Setup command** | `dotnet restore golden-template-csharp.sln && dotnet build golden-template-csharp.sln --configuration Release --no-restore` |
| **Run command** | `dotnet test --solution golden-template-csharp.sln --configuration Release --no-build` |
| **Timeout** | `120` seconds |
| **Points** | `10` |

Save the test, return to the assignment, and confirm it appears in the test
list. The points are all-or-nothing because Classroom 50's Run command type
uses the process exit status. Its case-by-case Python test type is designed for
pytest, not xUnit.

## Why these commands

The setup command restores packages and builds the solution once. The run
command executes the `StatisticsTests` xUnit v3 test project through .NET 10's
Microsoft Testing Platform mode. The `--solution` option is required for a
solution-level test in that mode.

If the assignment contains only one test project, you may use this run command
instead:

```text
dotnet test --project tests/StatisticsTests/StatisticsTests.csproj --configuration Release --no-build
```

Test the exact command locally before saving it in Classroom 50.

## Partial credit

One Run command cannot split points among xUnit cases. If separate requirements
need independent scores, create multiple focused Run command tests, each with
its own meaningful filter or test project and point value. Avoid a command that
can succeed after running zero tests.

## Protect instructor-owned files

When students must not edit grading infrastructure, add protected paths such
as:

```text
tests/**
global.json
golden-template-csharp.sln
```

Do not protect source files that students are expected to change.

## Prove it works

Accept the assignment as a student, deliberately break a library method, and
push. The grading run must be red and award zero points. Repair the method and
push again; it should be green with the full point value.

A green `0/0` result means the assignment has no published grading tests. See
[Troubleshooting](troubleshooting.md) if that occurs. For command-line setup,
see [Writing tests with the CLI](writing-tests.md).
