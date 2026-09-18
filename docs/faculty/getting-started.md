# Getting Started with the C# Code Template

This guide explains how to turn the sample statistics solution into a different
C# assignment while keeping projects, references, tests, CI, and grading
commands aligned.

The central rule is that the solution and project files are executable project
maps. When a project, namespace, or source relationship changes, update its
`.csproj`, the solution, tests, CI, and student instructions together.

## Start by understanding the current layout

```text
src/
├── Library/
│   ├── Library.csproj             # Reusable assignment logic
│   └── Statistics.cs              # Mean and median implementation
└── ConsoleApplication/
    ├── ConsoleApplication.csproj  # Executable; references Library
    └── Program.cs                 # Reads ten integers and prints results
tests/
└── StatisticsTests/
    ├── StatisticsTests.csproj     # xUnit v3 test project; references Library
    └── StatisticsTests.cs         # Five public test cases
golden-template-csharp.sln         # Groups all three projects
global.json                        # .NET 10 SDK and test-runner selection
BUILDING.md                        # Concise restore/build/test commands
```

All projects target `net10.0`, enable nullable reference analysis, and enable
implicit global `using` directives. The test project uses xUnit v3 with
Microsoft Testing Platform (MTP), selected by `global.json`.

## Read the project files as the project map

The class library is intentionally independent:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net10.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
  </PropertyGroup>
</Project>
```

The console application declares its dependency on the library:

```xml
<ItemGroup>
  <ProjectReference Include="..\Library\Library.csproj" />
</ItemGroup>
```

The test project similarly references the library under test and carries the
test packages:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Test.Sdk" Version="18.10.1" />
  <PackageReference Include="xunit.v3" Version="4.0.1" />
  <PackageReference Include="xunit.runner.visualstudio" Version="4.0.0" />
  <PackageReference Include="coverlet.collector" Version="10.0.1" />
</ItemGroup>

<ItemGroup>
  <ProjectReference Include="..\..\src\Library\Library.csproj" />
</ItemGroup>
```

`PrivateAssets` and `IncludeAssets` metadata in the real project prevent runner
and coverage tooling from leaking into projects that reference the test
assembly. Keep that metadata when updating those packages.

Finally, [`global.json`](../../global.json) opts .NET 10's `dotnet test` command
into MTP:

```json
{
  "sdk": {
    "version": "10.0.100",
    "rollForward": "latestFeature",
    "allowPrerelease": false
  },
  "test": {
    "runner": "Microsoft.Testing.Platform"
  }
}
```

With MTP selected, a solution is passed as `--solution
golden-template-csharp.sln`; the older positional `dotnet test
golden-template-csharp.sln` syntax selects the wrong execution path for this
configuration.

## Replace the assignment in a controlled order

### 1. Define the assignment contract

Before editing files, write down:

- Which projects and files students may edit.
- Required namespaces, classes, methods, parameters, and return types.
- Required exceptions or behavior for invalid input.
- Whether students may add NuGet packages or change public APIs.
- Which tests are public and whether hidden tests exist.

Put the student-facing version in
[`STUDENT_README.md`](../../STUDENT_README.md). A tested edge case must also be
a documented edge case.

### 2. Replace the reusable logic

Replace `src/Library/Statistics.cs` with the classes for the new exercise. Keep
logic that can be tested without console input in the library project whenever
the subject permits it.

For intentionally unfinished methods, fail explicitly:

```csharp
public static int CountMatches(IEnumerable<string> values, string target)
{
    throw new NotImplementedException("Implement CountMatches");
}
```

An empty placeholder that returns a plausible default can make untouched work
look like an incorrect implementation. `NotImplementedException` gives students
and graders a distinct signal.

If you rename the `Library` project or its namespace, update:

- The project filename and any `<ProjectReference>` paths.
- Namespace declarations and `using` directives.
- The `.sln` project entry, preferably through `dotnet sln`.
- CI, grading commands, and documentation that name the old project.

### 3. Decide whether to keep the console application

Keep `src/ConsoleApplication` when students must build or exercise a
command-line program. Put parsing and presentation there, while calling the
library for testable business logic.

Remove the console project when the assignment is purely a library exercise:

```powershell
dotnet sln golden-template-csharp.sln remove src/ConsoleApplication/ConsoleApplication.csproj
```

Then remove its directory and every instruction that tells students to run it.

### 4. Replace the tests

Replace `tests/StatisticsTests/StatisticsTests.cs` with tests for the documented
public behavior. xUnit discovers public methods marked with `[Fact]` or
`[Theory]`:

```csharp
using Library;

namespace QueueTests;

public class QueueTests
{
    [Fact]
    public void DequeueReturnsTheFirstEnqueuedValue()
    {
        Queue<string> queue = new();
        queue.Enqueue("first");
        queue.Enqueue("second");

        Assert.Equal("first", queue.Dequeue());
    }
}
```

Prefer one behavior per test and descriptive test names. Keep tests
deterministic: avoid live network calls, the real clock, uncontrolled
randomness, and writes outside a test-owned temporary directory.

Classroom 50's generic Run command test scores `dotnet test` by exit code, so
the suite is all-or-nothing unless you configure separate filtered runs or a
custom autograder. Design the grading weights accordingly.

### 5. Keep projects and the solution aligned

Add new projects with the CLI instead of editing solution GUIDs by hand:

```powershell
dotnet new classlib --framework net10.0 --output src/NewLibrary
dotnet sln golden-template-csharp.sln add src/NewLibrary/NewLibrary.csproj
dotnet add tests/StatisticsTests/StatisticsTests.csproj reference src/NewLibrary/NewLibrary.csproj
```

Remove obsolete project references and solution entries when deleting a
project. A solution entry does not create a dependency; dependencies belong in
`<ProjectReference>` elements.

### 6. Update NuGet packages deliberately

List direct package updates with:

```powershell
dotnet package list --project tests/StatisticsTests/StatisticsTests.csproj --outdated
```

Review release notes before accepting a major-version change. xUnit v3 changed
its package name from `xunit` to `xunit.v3` and uses MTP by default. Keep
`global.json`, the test project's `OutputType`, package references, and test
commands consistent with that runner choice.

Do not add a package merely because it is convenient during development. Every
package is restored in CI and the grading environment and becomes part of the
assignment's maintenance surface.

## Restore, build, run, and test

From the repository root:

```powershell
dotnet restore golden-template-csharp.sln
dotnet build golden-template-csharp.sln --configuration Release --no-restore
dotnet test --solution golden-template-csharp.sln --configuration Release --no-build
```

Run the console demonstration with:

```powershell
dotnet run --project src/ConsoleApplication --configuration Release
```

The sample expects ten integers and prints their mean and median.

Before publishing an assignment, verify both signals:

1. A known-correct solution builds and passes all tests.
2. A deliberately wrong solution produces a nonzero test result with a useful
   failing test name.

Also run the advisory repository check:

```powershell
python .github/scripts/check_core_standard.py
```

## Configure Classroom 50 consistently

The sample grading setup restores and builds once, then runs the xUnit suite:

```sh
dotnet restore golden-template-csharp.sln
dotnet build golden-template-csharp.sln --configuration Release --no-restore
dotnet test --solution golden-template-csharp.sln --configuration Release --no-build
```

Change the solution and project paths if you rename them. See
[Writing tests with the CLI](writing-tests.md) or
[Writing tests with the Web UI](writing-tests-web.md) for the complete setup.

## Common adaptation problems

### A namespace or type cannot be found

Confirm that the consuming project has the correct `<ProjectReference>`, then
check namespace declarations and `using` directives. Being present in the same
solution does not make one project's types visible to another.

### The solution builds but tests are not discovered

Confirm that the test project:

- Targets `net10.0` and has `<IsTestProject>true</IsTestProject>`.
- Uses `<OutputType>Exe</OutputType>` for xUnit v3.
- References `xunit.v3` and the intended test runner packages.
- Contains public `[Fact]` or `[Theory]` methods.
- Is included in `golden-template-csharp.sln`.

Run the project directly to separate discovery from solution traversal:

```powershell
dotnet test --project tests/StatisticsTests/StatisticsTests.csproj
```

### .NET 10 says the VSTest target is unsupported

Keep the `test.runner` value in `global.json` set to
`Microsoft.Testing.Platform` and use `dotnet test --solution ...` or `dotnet
test --project ...`. Do not use the older positional solution syntax.

### Restore succeeds locally but fails in CI

Check that CI installs .NET `10.0.x`, package versions are committed in the
project file, and no package depends on a private source configured only on
your machine.

### Old build output appears after projects change

Generated `bin/` and `obj/` directories can retain stale outputs. Run:

```powershell
dotnet clean golden-template-csharp.sln
```

If necessary, remove only this repository's generated `bin/` and `obj/`
directories, then restore and build again. Never commit those directories.

## Final adaptation checklist

- [ ] `STUDENT_README.md` names every required project and public API.
- [ ] Intentionally unfinished methods throw `NotImplementedException`.
- [ ] Every project targets `net10.0` and enables nullable analysis.
- [ ] Project references match the code's real dependencies.
- [ ] The solution contains every source and test project still in use.
- [ ] xUnit tests cover the documented normal and edge cases.
- [ ] Direct NuGet packages are current and intentionally included.
- [ ] Restore, Release build, and the complete test suite pass.
- [ ] A deliberately wrong solution fails with useful output.
- [ ] CI and Classroom 50 commands match the current solution layout.
- [ ] Student guides contain no stale instructions from another language or
      project structure.
