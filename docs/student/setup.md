# Student Build Guide

This project requires the .NET 10 SDK. It does not require a separate compiler,
package manager, or virtual environment: the SDK supplies the compiler, NuGet
client, build tools, application launcher, and test command.

Run every command below from the repository root, the directory containing
`golden-template-csharp.sln`.

## 1. Install the .NET 10 SDK

Download the **SDK** (not only the runtime) from the
[official .NET 10 download page](https://dotnet.microsoft.com/download/dotnet/10.0).
On Windows, you can instead use:

```powershell
winget install Microsoft.DotNet.SDK.10
```

After installation, open a new terminal and verify it:

```bash
dotnet --version
```

The result should begin with `10.`. The repository's `global.json` selects a
.NET 10 SDK and permits a newer .NET 10 feature band.

## 2. Restore dependencies

```bash
dotnet restore golden-template-csharp.sln
```

Restore downloads the packages declared by the test project, including xUnit.
Run it after cloning and whenever project package references change.

## 3. Build the solution

```bash
dotnet build golden-template-csharp.sln --configuration Release --no-restore
```

The solution builds the reusable `Library`, the `ConsoleApplication`, and the
`StatisticsTests` test project. Fix build errors before running tests.

## 4. Run the program

```bash
dotnet run --project src/ConsoleApplication --configuration Release
```

Enter ten integers separated by spaces or newlines. The program prints their
mean and median. Invalid input produces an error and a nonzero exit code.

## 5. Run the tests

```bash
dotnet test --solution golden-template-csharp.sln --configuration Release --no-build
```

The repository uses xUnit v3 and .NET's Microsoft Testing Platform mode. The
starter template currently contains five tests. A successful run reports all
five as passed.

During development, repeat this shorter cycle:

```bash
dotnet build golden-template-csharp.sln --configuration Release
dotnet test --solution golden-template-csharp.sln --configuration Release --no-build
```

## Project layout

```text
golden-template-csharp.sln
src/
  Library/                 reusable statistics code
  ConsoleApplication/      command-line application
tests/
  StatisticsTests/         xUnit tests for the library
```

Do not commit generated `bin/` or `obj/` directories.

## Troubleshooting

### `dotnet` is not recognized

Install the .NET 10 SDK, close and reopen the terminal, and run `dotnet
--version` again. If the command still fails, confirm the SDK installation
directory is on your system `PATH`.

### `NETSDK1045` says the SDK does not support .NET 10

You have an older SDK. Install .NET 10 and check `dotnet --list-sdks`. At least
one listed version must begin with `10.`.

### Restore cannot reach NuGet

Check your network connection and try `dotnet restore
golden-template-csharp.sln` again. On a managed campus network, ask your
instructor whether a proxy or approved package source is required.

### The build succeeds but tests do not run

Run the solution form exactly as shown:

```bash
dotnet test --solution golden-template-csharp.sln --configuration Release
```

The `global.json` file selects Microsoft Testing Platform. Keep it in the
repository root and run the command from that directory.

### A test fails

Read the named test and its expected and actual values. Make one focused code
change, rebuild, and rerun the suite. Do not modify or remove instructor tests
unless the assignment explicitly requires test changes.
