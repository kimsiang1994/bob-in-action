# ASP.NET Core (invoice preview)

Bob's bounded proof that the invoice-preview feature can be rebuilt as an ASP.NET Core minimal API (`Program.cs`, route `POST /api/invoice-preview`), with xUnit contract tests in `tests/PreviewApi.Tests/`. It is one feature, not a full migration.

## Build and test

Needs the .NET 10 SDK (NuGet packages are restored on first build). From the repository root:

```powershell
dotnet build targets/dotnet/PreviewApi.csproj --configuration Release --nologo
dotnet test targets/dotnet/tests/PreviewApi.Tests/PreviewApi.Tests.csproj --configuration Release --nologo
```

Verified here: `Passed! - Failed: 0, Passed: 32` with the .NET 10 SDK.

## Run

`dotnet run --configuration Release --project targets/dotnet/PreviewApi.csproj --urls http://127.0.0.1:18083`
