# 📋 Changelog
All changes to `MyCustom.NET.Sdk` are documented here. This project follows [Semantic Versioning 2.0.0](https://semver.org).

See the [README](README.md) for full usage details.

## 🎯 [1.0.4] - 2026-04-17
Updates to NuGet package references. See below for details.

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
    "MyCustom.NET.Sdk": "1.0.4"
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

See the [README](README.md) for full SDK details.

## 🎯 [1.0.3] - 2026-04-03
Updates to NuGet package references. See below for details.

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
    "MyCustom.NET.Sdk": "1.0.3"
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

See the [README](README.md) for full SDK details.

## 🎯 [1.0.2] - 2026-04-01
Updates to NuGet package references. See below for details.

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
    "MyCustom.NET.Sdk": "1.0.2"
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

See the [README](README.md) for full SDK details.

## 🎯 [1.0.1] - 2026-03-30
Initial release of `MyCustom.NET.Sdk`.

### 🎉 What's New

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
    "MyCustom.NET.Sdk": "1.0.1"
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

See the [README](README.md) for full SDK details.
