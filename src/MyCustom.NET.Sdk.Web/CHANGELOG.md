# 📋 Changelog
All changes to `MyCustom.NET.Sdk.Web` are documented here. This project follows [Semantic Versioning 2.0.0](https://semver.org).

See the [README](README.md) for full usage and SDK details.

## 🎯 [1.0.7] - 2026-08-08
`MyCustom.NET.Sdk.Web` 1.0.8 incorporates `MyCustom.NET.Sdk` 1.0.8 as its base. All of the following are a direct result of that import.

 * SDK package references updated to include `IsImplicitlyDefined="true"`. See https://learn.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files#isimplicitlydefined-metadata for more details.
 * Added additional future work items.

### 🎉 What's New

#### C# Compiler
None.

#### MSBuild & Code Analysis
None.

#### NuGet
None.

#### Source Link & Supply Chain Security
None.

#### Analyzers
None.

### 💥 Breaking Changes
None.

### 🩹 Bug Fixes
None.

### ⚠️ Known Issues
None.

### ⬆️ Upgrading
To consume the SDK, add it to `global.json` at the root of your repository:
```json
{
  "msbuild-sdks": {
    "MyCustom.NET.Sdk": "1.0.8"
  }
}
```

Then reference it in your `.csproj`:
```xml
<Project Sdk="MyCustom.NET.Sdk">

  <PropertyGroup>

    <!-- Usual properties and overrides go here -->

  </PropertyGroup>

</Project>
```

## 🎯 [1.0.7] - 2026-08-07
`MyCustom.NET.Sdk.Web` 1.0.7 incorporates `MyCustom.NET.Sdk` 1.0.7 as its base. All of the following are a direct result of that import.

 * The `<Title>` property was deleted to streamline project metadata since they're effectively ignored by the majority of nuget client UIs.
 * Fixed errant solution file path reference to Directory.Packages.props.
 * Upgraded actions/checkout to v7 in CI workflow.
 * Bumped .NET SDK version to 10.0.302 in global.json for latest patch updates.
 * Updated actions/setup-dotnet@v6 to v7 in CI workflow. See https://github.com/actions/setup-dotnet/releases for details.
 * Updates to Analyzers. See below for details.
 
### 🎉 What's New

#### C# Compiler
None.

#### MSBuild & Code Analysis
None.

#### NuGet
None.

#### Source Link & Supply Chain Security
None.

#### Analyzers
* `Meziantou.Analyzer` - Updated NuGet package from 3.0.114 to 3.0.140. See https://github.com/meziantou/Meziantou.Analyzer/releases/tag/3.0.140 for change details.
* `Microsoft.CodeAnalysis.BannedApiAnalyzers` - Updated NuGet package from 3.3.4 to 5.6.0.

### 💥 Breaking Changes
None.

### 🩹 Bug Fixes
None.

### ⚠️ Known Issues
None.

### ⬆️ Upgrading
To consume the SDK, add it to `global.json` at the root of your repository:
```json
{
  "msbuild-sdks": {
    "MyCustom.NET.Sdk": "1.0.7"
  }
}
```

Then reference it in your `.csproj`:
```xml
<Project Sdk="MyCustom.NET.Sdk">

  <PropertyGroup>

    <!-- Usual properties and overrides go here -->

  </PropertyGroup>

</Project>
```

## 🎯 [1.0.6] - 2026-06-25
`MyCustom.NET.Sdk.Web` 1.0.6 incorporates `MyCustom.NET.Sdk` 1.0.6 as its base. All of the following are a direct result of that import.

 * Fixed a bug in pack-and-publish-nuget-package.yml that prevented the workflow from getting the PackageId, PackageVersion and PackageOutputPath from the project file.
 * Updated .NET SDK version from 10.0.201 to 10.0.300 in global.json.
 * Removed unused Directory.Build.targets.
 * Enabled [CPM](https://learn.microsoft.com/en-us/nuget/consume-packages/central-package-management) and [SBOM](https://devblogs.microsoft.com/engineering-at-microsoft/microsoft-open-sources-software-bill-of-materials-sbom-generation-tool/) generation in project.
 * Additions and updates to Analyzers. See below for details.

### 🎉 What's New

#### C# Compiler
None.

#### MSBuild & Code Analysis
None.

#### NuGet
None.

#### Source Link & Supply Chain Security
None.

#### Analyzers
* `Meziantou.Analyzer` - Updated NuGet package from 3.0.71 to 3.0.114. See https://github.com/meziantou/Meziantou.Analyzer/releases/tag/3.0.114 for change details.
* `AsyncFixer` - Added [`AsyncFixer`](https://github.com/semihokur/AsyncFixer) NuGet package version 2.1.0 to help developers find and correct common `async/await` misuses (anti-patterns) and, when possible, offer automatic fixes.
* `Microsoft.CodeAnalysis.BannedApiAnalyzers` - Added [`Microsoft.CodeAnalysis.BannedApiAnalyzers`](https://github.com/dotnet/roslyn/tree/main/src/RoslynAnalyzers/Microsoft.CodeAnalysis.BannedApiAnalyzers) NuGet package version 3.3.4 to allow banning specific APIs from being used in your codebase, either by specifying them directly or by referencing an external configuration file. Useful for enforcing architectural boundaries, preventing usage of dangerous or deprecated APIs and maintaining a clean and consistent codebase.
* `Microsoft.VisualStudio.Threading.Analyzers` - Added [`Microsoft.VisualStudio.Threading.Analyzers`](https://microsoft.github.io/vs-threading/analyzers/index.html) NuGet package version 18.7.23 to provide static code analyzers to detect common mistakes or potential issues regarding threading and async coding.
* `Roslynator.Analyzers` - Added [`Roslynator.Analyzers`](https://github.com/dotnet/roslynator) NuGet package version 4.15.0 to provide a collection of 200+ analyzers for C#, powered by Roslyn.

### 💥 Breaking Changes
None.

### 🩹 Bug Fixes
None.

### ⚠️ Known Issues
None.

### ⬆️ Upgrading
To consume the SDK, add it to `global.json` at the root of your repository:
```json
{
  "msbuild-sdks": {
    "MyCustom.NET.Sdk": "1.0.6"
  }
}
```

Then reference it in your `.csproj`:
```xml
<Project Sdk="MyCustom.NET.Sdk">

  <PropertyGroup>

    <!-- Usual properties and overrides go here -->

  </PropertyGroup>

</Project>
```

## 🎯 [1.0.5] - 2026-05-06
Updates to NuGet package references. See below for details.

### 🎉 What's New
`MyCustom.NET.Sdk.Web` 1.0.5 incorporates `MyCustom.NET.Sdk` 1.0.5 as its base. All of the following are a direct result of that import.

#### C# Compiler
None.

#### MSBuild & Code Analysis
None.

#### NuGet
None.

#### Source Link & Supply Chain Security
None.

#### Analyzers
* `Meziantou.Analyzer` - Updated NuGet package from 3.0.49 to 3.0.71. See https://github.com/meziantou/Meziantou.Analyzer/releases/tag/3.0.71 for change details.

### 💥 Breaking Changes
None.

### 🩹 Bug Fixes
None.

### ⚠️ Known Issues
None.

### ⬆️ Upgrading
To consume the SDK, add it to `global.json` at the root of your repository:
```json
{
  "msbuild-sdks": {
    "MyCustom.NET.Sdk.Web": "1.0.5"
  }
}
```

Then reference it in your `.csproj`:
```xml
<Project Sdk="MyCustom.NET.Sdk.Web">

  <PropertyGroup>

    <!-- Usual properties and overrides go here -->

  </PropertyGroup>

</Project>
```

## 🎯 [1.0.4] - 2026-04-17
Updates to NuGet package references. See below for details.

### 🎉 What's New
`MyCustom.NET.Sdk.Web` 1.0.4 incorporates `MyCustom.NET.Sdk` 1.0.4 as its base. All of the following are a direct result of that import.

#### C# Compiler
None.

#### MSBuild & Code Analysis
None.

#### NuGet
None.

#### Source Link & Supply Chain Security
None.

#### Analyzers
* `Meziantou.Analyzer` - Updated NuGet package from 3.0.44 to 3.0.49. See https://github.com/meziantou/Meziantou.Analyzer/releases/tag/3.0.49 for change details.

### 💥 Breaking Changes
None.

### 🩹 Bug Fixes
None.

### ⚠️ Known Issues
None.

### ⬆️ Upgrading
To consume the SDK, add it to `global.json` at the root of your repository:
```json
{
  "msbuild-sdks": {
    "MyCustom.NET.Sdk.Web": "1.0.4"
  }
}
```

Then reference it in your `.csproj`:
```xml
<Project Sdk="MyCustom.NET.Sdk.Web">

  <PropertyGroup>

    <!-- Usual properties and overrides go here -->

  </PropertyGroup>

</Project>
```

## 🎯 [1.0.3] - 2026-04-03
Updates to NuGet package references. See below for details.

### 🎉 What's New
`MyCustom.NET.Sdk.Web` 1.0.3 incorporates `MyCustom.NET.Sdk` 1.0.3 as its base. All of the following are a direct result of that import.

#### C# Compiler
None.

#### MSBuild & Code Analysis
None.

#### NuGet
None.

#### Source Link & Supply Chain Security
None.

#### Analyzers
* `Meziantou.Analyzer` - Updated NuGet package from 3.0.41 to 3.0.44. See https://github.com/meziantou/Meziantou.Analyzer/releases for change details.

### 💥 Breaking Changes
None.

### 🩹 Bug Fixes
None.

### ⚠️ Known Issues
None.

### ⬆️ Upgrading
To consume the SDK, add it to `global.json` at the root of your repository:
```json
{
  "msbuild-sdks": {
    "MyCustom.NET.Sdk.Web": "1.0.3"
  }
}
```

Then reference it in your `.csproj`:
```xml
<Project Sdk="MyCustom.NET.Sdk.Web">

  <PropertyGroup>

    <!-- Usual properties and overrides go here -->

  </PropertyGroup>

</Project>
```

## 🎯 [1.0.2] - 2026-04-01
Updates to NuGet package references. See below for details.

### 🎉 What's New
`MyCustom.NET.Sdk.Web` 1.0.2 incorporates `MyCustom.NET.Sdk` 1.0.2 as its base. All of the following are a direct result of that import.

#### C# Compiler
None.

#### MSBuild & Code Analysis
None.

#### NuGet
None.

#### Source Link & Supply Chain Security
None.

#### Analyzers
* `Meziantou.Analyzer` - Updated NuGet package from 3.0.24 to 3.0.41. See https://github.com/meziantou/Meziantou.Analyzer/releases for change details.

### 💥 Breaking Changes
None.

### 🩹 Bug Fixes
None.

### ⚠️ Known Issues
None.

### ⬆️ Upgrading
To consume the SDK, add it to `global.json` at the root of your repository:
```json
{
  "msbuild-sdks": {
    "MyCustom.NET.Sdk.Web": "1.0.2"
  }
}
```

Then reference it in your `.csproj`:
```xml
<Project Sdk="MyCustom.NET.Sdk.Web">

  <PropertyGroup>

    <!-- Usual properties and overrides go here -->

  </PropertyGroup>

</Project>
```

## 🎯 [1.0.1] - 2026-03-30
Initial release of `MyCustom.NET.Sdk.Web`.

### 🎉 What's New
`MyCustom.NET.Sdk.Web` 1.0.1 incorporates `MyCustom.NET.Sdk` 1.0.1 as its base. All of the following are a direct result of that import.

#### C# Compiler
* Nullable reference types enabled (`Nullable=enable`)
* All .NET code quality and style analyzers enabled up to the latest released SDK (`AnalysisLevel=latest-all`)
* Compiler warnings treated as errors (`TreatWarningsAsErrors=true`)
* C# strict mode enabled (`Features=strict`), activating additional breaking-change warnings that are off by default
* PDBs embedded directly into output assemblies (`DebugType=embedded`) for Source Link compatibility
* Analyzer performance telemetry emitted during builds (`ReportAnalyzer=true`)

#### MSBuild & Code Analysis
* MSBuild warnings treated as errors (`MSBuildTreatWarningsAsErrors=true`)
* Roslyn analyzer warnings treated as errors (`CodeAnalysisTreatWarningsAsErrors=true`)
* EditorConfig code style rules enforced at build time (`EnforceCodeStyleInBuild=true`)
* XML documentation file generation enabled (`GenerateDocumentationFile=true`)
* Implicit global using directives enabled (`ImplicitUsings=enable`)
* Visual Studio build acceleration enabled (`AccelerateBuildsInVisualStudio=true`)

#### NuGet
* Projects are non-packable by default (`IsPackable=false`)
* NuGet package validation enabled (`EnablePackageValidation=true`)
* Static graph evaluation enabled for restore (`RestoreUseStaticGraphEvaluation=true`)
* Global property serialization during restore enabled (`RestoreSerializeGlobalProperties=true`), working around [NuGet/Home#14731](https://github.com/nuget/home/issues/14731)

#### Source Link & Supply Chain Security
* Repository URL embedded in package metadata (`PublishRepositoryUrl=true`)
* SBOM generation enabled via `Microsoft.Sbom.Targets` 4.1.5, producing a Software Bill of Materials in SPDX format as part of every build

#### Analyzers
* `Meziantou.Analyzer` 3.0.24 - a Roslyn analyzer enforcing good practices across design, usage, security, performance and style
* `Microsoft.CodeAnalysis.BannedApiAnalyzers` 3.3.4 - allows banning specific APIs from use in the codebase, useful for enforcing architectural boundaries and preventing usage of dangerous or deprecated APIs

### 💥 Breaking Changes
None. This is the initial release.

### 🩹 Bug Fixes
None. This is the initial release.

### ⚠️ Known Issues
None.

### ⬆️ Upgrading
This is the initial release. To consume the SDK, add it to `global.json` at the root of your repository:
```json
{
  "msbuild-sdks": {
    "MyCustom.NET.Sdk.Web": "1.0.1"
  }
}
```

Then reference it in your `.csproj`:
```xml
<Project Sdk="MyCustom.NET.Sdk.Web">

  <PropertyGroup>

    <!-- Usual properties and overrides go here -->

  </PropertyGroup>

</Project>
```
