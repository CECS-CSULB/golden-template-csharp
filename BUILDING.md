# Building the C# Demo

The project uses the .NET 10 SDK and xUnit. From the repository root:

~~~powershell
dotnet restore golden-template-csharp.sln
dotnet build golden-template-csharp.sln --configuration Release --no-restore
dotnet test --solution golden-template-csharp.sln --configuration Release --no-build
~~~

Run the demo and enter ten integers separated by spaces or newlines:

~~~powershell
dotnet run --project src/ConsoleApplication --configuration Release
~~~

The console application prints the mean and median after it has read all ten
values. Invalid input produces an error message and a nonzero exit code.
