# 🐛 Debugging
MSBuild SDK debugging can feel opaque, particularly when troubleshooting unexpected property values or import ordering issues. Fortunately, MSBuild ships with several diagnostic tools that can surface what is actually happening at evaluation and execution time.

## Preprocessor Output
The `-preprocess` switch (alias `-pp`) outputs the fully expanded project file after all SDKs, props and targets have been imported, without running a build. This makes it useful for inspecting import order, spotting duplicate or overridden property definitions and understanding exactly what MSBuild is working with:
```powershell
msbuild MyProject.csproj -preprocess:MyProject.evaluated.xml
```

It is also available via `dotnet msbuild`:
```powershell
dotnet msbuild MyProject.csproj -pp:MyProject.evaluated.xml
```

The output is a single large XML file representing the fully merged project. It can be verbose for SDK-based projects given how much the SDK contributes, but it is invaluable for tracing the complete structure of your project after all imports have been resolved.

Because `-preprocess` does not execute any build targets or tasks, it is safe to use as a lightweight pre-flight check before committing to a potentially expensive build.

## Property Resolution
For quick spot-checks, `-getProperty` is a more targeted alternative to scanning a large preprocessor output. It accepts a single property name or a comma-delimited list of names and prints their resolved values for a given project without running a build.

When querying a **single property**, the output is a plain string:
```powershell
dotnet msbuild MyProject.csproj -getProperty:TargetFramework
# net10.0
```

When querying **multiple properties**, the output is a JSON document:
```powershell
dotnet msbuild MyProject.csproj -getProperty:TargetFramework,Nullable,ImplicitUsings
# { "Properties": { "TargetFramework": "net10.0", "Nullable": "enable", "ImplicitUsings": "enable" } }
```

To capture multiple values in PowerShell, pipe through `ConvertFrom-Json` and destructure from the `Properties` object:
```powershell
$targetFramework, $nullable, $implicitUsings =
    dotnet msbuild MyProject.csproj -getProperty:TargetFramework,Nullable,ImplicitUsings |
    ConvertFrom-Json |
    % { $_.Properties.TargetFramework, $_.Properties.Nullable, $_.Properties.ImplicitUsings }
```

Like `-preprocess`, `-getProperty` does not execute any build targets or tasks, making it well-suited for verifying that a set of properties resolve to their expected values in your specific environment before going through a full build. This is particularly useful given that a property's actual resolved value may not match what documentation says, especially across .NET versions or in cases where Visual Studio project templates set values that appear to be SDK defaults but are not. Rather than tracing to the answer through a binlog or hunting through a large preprocessor output, `-getProperty` gives you the **final** resultant value directly.
## Binary Log
For deeper inspection, particularly when you need to understand target execution order, why a property changed mid-build or what tasks ran and in what sequence, generating a binary log is the most comprehensive option. The `-binaryLogger` switch (alias `-bl`) produces a `.binlog` file containing full evaluation and execution details:
```powershell
dotnet build -binaryLogger
```

It is also available via `msbuild` directly:
```powershell
msbuild MyProject.csproj -binaryLogger
```

The resulting `.binlog` file can be opened in the [MSBuild Structured Log Viewer](https://msbuildlog.com) for a fully structured, searchable view of everything MSBuild did during the build.

---

For further reading on the `msbuild` and `dotnet` CLIs and related tooling, see [Useful Resources](useful-resources.md).
