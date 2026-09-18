# Writing Classroom 50 tests for C#

Classroom 50 stores grading instructions in the configuration repository. Test
files in the assignment template are not run until an assignment test tells the
runner what command to execute.

## Use a run test for this project

Classroom 50 provides `run`, `io`, and Python-specific test types. A C# xUnit
suite should use `run`. The command receives all assigned points when it exits
successfully and zero when restore, compilation, or any test fails.

Add the template's grading test with:

```sh
gh teacher assignment test add <org> <classroom> <slug> \
  --name "xUnit suite" --type run \
  --setup "dotnet restore golden-template-csharp.sln && dotnet build golden-template-csharp.sln --configuration Release --no-restore" \
  --run "dotnet test --solution golden-template-csharp.sln --configuration Release --no-build" \
  --timeout 120 --points 10
```

The setup command restores NuGet packages and compiles all three projects. The
run command uses the repository's `global.json`, which selects Microsoft
Testing Platform for .NET 10 and xUnit v3.

## Choose commands that match the repository

This template contains:

```text
golden-template-csharp.sln
src/Library/Library.csproj
src/ConsoleApplication/ConsoleApplication.csproj
tests/StatisticsTests/StatisticsTests.csproj
```

Keep solution-level commands when the assignment depends on all projects. A
smaller assignment may target one test project instead:

```sh
dotnet test --project tests/StatisticsTests/StatisticsTests.csproj \
  --configuration Release --no-build
```

Do not use the older positional solution syntax in Microsoft Testing Platform
mode. Use `--solution` for a solution or `--project` for one project.

## Weighting and partial credit

A `run` test is all-or-nothing. To award independent points for distinct
requirements, create separate run tests with focused test filters or separate
test projects. Each command must be meaningful on its own; avoid splitting a
single behavior into artificial fragments merely to manufacture partial
credit.

For example, if the test names use traits, a focused command can pass an xUnit
filter after `--`:

```sh
dotnet test --project tests/StatisticsTests/StatisticsTests.csproj \
  --configuration Release --no-build -- --filter-query "/[Category=Median]"
```

Verify any filter locally before publishing it. An invalid filter can run zero
tests and create misleading results.

## Timeouts

The default Classroom 50 timeout is short and includes setup. NuGet restore can
take longer on a clean runner, so this template uses `--timeout 120`. Keep the
timeout high enough for a cold restore but low enough to stop a hung program.

## Protect instructor-owned files

If students must not change tests or project configuration, enforce that rule
with Classroom 50 protected paths in addition to stating it in the assignment.
Typical protected paths for this template are:

```text
tests/**
global.json
golden-template-csharp.sln
```

Protect only files that truly belong to the instructor. Students must still be
able to edit the source files required by the assignment.

## Verify the grader before release

List the configured tests:

```sh
gh teacher assignment test list <org> <classroom> <slug>
```

Then accept the assignment yourself and make two submissions:

1. A deliberately broken implementation that must produce a red run and `0/10`.
2. A correct implementation that must produce a green run and `10/10`.

A green `0/0` result means no grading tests were published. It does not prove
the assignment works.

## Useful test types for other assignments

- `run`: any command whose exit status determines success; use this for .NET.
- `io`: feed input to a command and compare its output; useful for tightly
  specified console programs.
- `python`: pytest-aware scoring that can split points among collected cases;
  it is not the xUnit integration path.

See the [Classroom 50 CLI Teacher Guide](https://github.com/foundation50/classroom50/wiki/CLI-Teacher-Guide)
for the current command reference and [Troubleshooting](troubleshooting.md) for
common failure modes.
