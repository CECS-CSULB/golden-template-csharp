# Guidance for Faculty

This template contains a C# solution targeting .NET 10.0, with a reusable
class library, a console application, and an xUnit v3 test project. GitHub
Actions restores, builds, and tests the solution on every push. The repository
can be used with Classroom 50 as an auto-graded assignment template or given
to students to fork when auto-grading is not needed.

The [faculty documentation](docs/faculty/README.md) explains how to adapt the
starter code, write tests, and optionally configure Classroom 50. Before
publishing an assignment to students, remove `docs/faculty` and this faculty
section if they would cause confusion.

## Faculty To-Do

1. Replace the sample statistics code under `src/` with the assignment's
   starter code.
2. Replace the xUnit tests under `tests/` and keep project references aligned
   with the source projects they exercise.
3. Edit [STUDENT_README.md](STUDENT_README.md) with assignment-specific
   requirements and review the guides in
   [docs/student](docs/student/README.md).
4. Review package references in each `.csproj`; retain only dependencies the
   assignment uses.
5. Review the warning below and decide whether to keep the student publishing
   guide.
6. Read the [faculty documentation](docs/faculty/README.md) to understand the
   solution layout, CI commands, and optional Classroom 50 integration.
7. Create a GitHub template repository for the assignment, if needed.
8. If AI assistance is allowed, review or adapt the root
   [Verification Log](VERIFICATION-LOG.md); a clean faculty copy is available
   at [docs/faculty/VERIFICATION-LOG.md](docs/faculty/VERIFICATION-LOG.md).
9. Remove faculty-only documentation, then commit and push the assignment.

## Language-specific notes

The solution uses the .NET 10 SDK and xUnit.net v3. Its projects are:

| Project | Purpose |
|---|---|
| [`src/Library/Library.csproj`](src/Library/Library.csproj) | Reusable assignment logic. The sample `Statistics` class implements mean and median. |
| [`src/ConsoleApplication/ConsoleApplication.csproj`](src/ConsoleApplication/ConsoleApplication.csproj) | Demonstration program. It references `Library`. |
| [`tests/StatisticsTests/StatisticsTests.csproj`](tests/StatisticsTests/StatisticsTests.csproj) | Five xUnit tests. It references `Library`. |

[`golden-template-csharp.sln`](golden-template-csharp.sln) groups all three
projects. Every project targets `net10.0`, enables nullable reference analysis,
and uses implicit global `using` directives.

[`global.json`](global.json) selects a .NET 10 SDK and opts `dotnet test` into
Microsoft Testing Platform, which is embedded by xUnit v3. Run the complete
local workflow from the repository root:

```powershell
dotnet restore golden-template-csharp.sln
dotnet build golden-template-csharp.sln --configuration Release --no-restore
dotnet test --solution golden-template-csharp.sln --configuration Release --no-build
```

Run the sample application with:

```powershell
dotnet run --project src/ConsoleApplication --configuration Release
```

When adding or renaming projects, update the solution, `ProjectReference`
elements, GitHub Actions commands, Classroom 50 commands, and documentation
together. See [BUILDING.md](BUILDING.md) for the concise build instructions.

## Warning about student documentation

The file [docs/student/publishing.md](docs/student/publishing.md) walks students
through publishing an approved copy of their completed assignment to a public
GitHub profile. It tells them to wait until the semester is over, obtain the
instructor's permission, and remove private course material first.

If students should not publish completed work, replace that guide with the
course's policy. Consider explaining how students may describe the work on a
résumé or portfolio without releasing the source.

# Guidance for Students

This assignment is derived from the CSULB CECS Department Golden Template, a
starting point for faculty to create programming assignments that use a
repeatable project layout, automated tests, and continuous integration.

Read [STUDENT_README.md](STUDENT_README.md) first for the assignment
requirements. Then use [docs/student/README.md](docs/student/README.md) for
setup, Git, development, README-writing, and publishing guides.
