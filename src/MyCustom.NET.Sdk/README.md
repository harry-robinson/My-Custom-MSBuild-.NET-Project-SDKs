# 📦 `MyCustom.NET.Sdk`
`MyCustom.NET.Sdk` is a custom MSBuild project SDK for .NET development. It wraps `Microsoft.NET.Sdk` and layers a set of opinionated defaults on top, covering compiler strictness, code analysis, documentation generation, NuGet packaging, supply chain security and Source Link support.

The goal is to encode best practices once at the SDK level rather than duplicating them across every project file. A consuming project gets a production-grade, fully analysed build out of the box with a minimal project file and any project that deviates from the defaults does so explicitly and intentionally.

It can be used as-is as a drop-in replacement for `Microsoft.NET.Sdk` or extended further as a foundation for building your own custom SDK. `MyCustom.NET.Sdk.Web` takes the latter approach, extending this SDK for ASP.NET Core projects with web-specific configuration on top of the same foundation.

This SDK is part of a broader educational project aimed at demystifying custom MSBuild SDK authoring — a topic with sparse official documentation and few real-world examples to learn from. The full source, documentation and authoring guides are available on [GitHub](https://github.com/harry-robinson/My-Custom-MSBuild-.NET-Project-SDKs).

## 🔌 Usage
The recommended way to reference the SDK is via `global.json` at the root of your repository. This ensures all projects use the same version without specifying it in every project file:
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
```

Alternatively, the version can be specified inline in the project file, though this is not recommended for repositories with multiple projects as it requires the version to be kept in sync across every project file:
```xml
<Project Sdk="MyCustom.NET.Sdk/1.0.3">
```

## 📖 `Sdk.props` Reference
The following table documents every property and package reference set in `Sdk.props`. Commented-out entries are included where they represent defaults worth being aware of or options worth considering.

### C# Compiler Options
| Property | Value | Description |
|---|---|---|
| `Nullable` | `enable` | Enables C# nullable reference types. Uninitialized or potentially-null references produce warnings (or errors, given `TreatWarningsAsErrors`). |
| `AllowUnsafeBlocks` | *(commented out)* | Unsafe code is disabled by default. Enable explicitly only if your project requires it. |
| `LangVersion` | *(commented out)* | Defaults to the latest language version for the target framework. Override with `latest` or `preview` only if intentional. Read the documentation carefully before setting this. |
| `AnalysisLevel` | `latest-all` | Enables all .NET code quality and style analyzers up to the latest released SDK. `preview-all` is available as an alternative to include analyzers from the latest preview release. |
| `TreatWarningsAsErrors` | `true` | Instructs the C# compiler to treat all compiler warnings as errors. Note this is distinct from `MSBuildTreatWarningsAsErrors`, which operates at the MSBuild level. See [this post](https://anthonysimmon.com/treatwarningsaserrors-and-warnaserror-are-not-the-same/) for the distinction. |
| `ReportAnalyzer` | `true` | Emits analyzer performance telemetry during the build. Useful for diagnosing slow builds caused by expensive analyzers. |
| `DebugType` | `embedded` | Embeds the PDB directly into the output assembly rather than producing a separate `.pdb` file. Works in conjunction with `EmbedUntrackedSources` and `PublishRepositoryUrl` for Source Link support. |
| `Deterministic` | *(commented out)* | Deterministic compilation is enabled by default for modern .NET projects. Explicitly setting it is unnecessary unless overriding a parent SDK that disables it. |
| `Features` | `strict` | Enables the C# compiler's strict mode, which activates additional breaking-change warnings that are off by default. See [meziantou's post](https://www.meziantou.net/csharp-compiler-strict-mode.htm) for details. |

### MSBuild Properties for .NET SDK Projects
| Property | Value | Description |
|---|---|---|
| `MSBuildTreatWarningsAsErrors` | `true` | Instructs MSBuild to treat all MSBuild warnings as errors. Operates independently of the C# compiler's `TreatWarningsAsErrors`. Both are required to achieve full warning-as-error coverage across the build. |
| `GenerateDocumentationFile` | `true` | Generates an XML documentation file from `///` doc comments. Required for NuGet packages that surface IntelliSense documentation to consumers. |
| `ImplicitUsings` | `enable` | Enables the implicit `global using` directives injected by the .NET SDK, reducing boilerplate `using` statements for commonly used namespaces. |
| `CodeAnalysisTreatWarningsAsErrors` | `true` | Treats code analysis (Roslyn analyzer) warnings as errors. Complements `TreatWarningsAsErrors`, which covers compiler warnings. |
| `EnforceCodeStyleInBuild` | `true` | Enforces `.editorconfig` code style rules as part of the build, not just in the IDE. Style violations are surfaced as warnings or errors depending on their configured severity. |
| `EnableNETAnalyzers` | *(commented out)* | Enabled by default for projects targeting .NET 5+. No need to set explicitly unless overriding. |
| `AccelerateBuildsInVisualStudio` | `true` | Enables Visual Studio build acceleration for SDK-style projects, reducing incremental build times in the IDE by skipping up-to-date projects more aggressively. |

### NuGet - Pack, Restore & Audit
| Property | Value | Description |
|---|---|---|
| `IsPackable` | `false` | Prevents consuming projects from being packed as NuGet packages by default. Override in individual projects that should produce packages. |
| `EnablePackageValidation` | `true` | Enables NuGet package validation, which checks for API compatibility issues between package versions. Catches breaking changes at pack time rather than at consumer build time. |
| `RestoreUseStaticGraphEvaluation` | `true` | Uses MSBuild's static graph evaluation during restore, improving correctness and performance for large solutions. |
| `RestoreSerializeGlobalProperties` | `true` | Ensures global properties are correctly serialized during restore. Works around a NuGet issue where certain global properties were not being passed through correctly. See [NuGet/Home#14731](https://github.com/nuget/home/issues/14731). |
| `NuGetAudit` | *(commented out)* | Enabled by default for .NET 8+ projects. Audits package dependencies for known security vulnerabilities during restore. |
| `NuGetAuditMode` | *(commented out)* | Defaults to `all` for .NET 10+ projects. Controls whether direct dependencies only or all transitive dependencies are audited. |
| `NuGetAuditLevel` | *(commented out)* | Defaults to `low`. Controls the minimum vulnerability severity level that triggers an audit warning. |

### Source Link & SBOM
| Property | Value | Description |
|---|---|---|
| `PublishRepositoryUrl` | `true` | Embeds the repository URL in the NuGet package metadata, enabling Source Link to map symbols back to source. Requires a source control provider package (e.g. `Microsoft.SourceLink.GitHub`). |
| `EmbedUntrackedSources` | *(commented out)* | Embeds source files not tracked by source control (e.g. generated files) directly into the PDB, ensuring they are available for debugging even when not in the repository. Enabled by default starting with the .NET 8 SDK. |
| `GenerateSBOM` | `true` | Triggers SBOM generation as part of the build via `Microsoft.Sbom.Targets`. Produces a Software Bill of Materials in SPDX format, documenting all dependencies included in the build output. |

### Analyzers
| Package | Version | Description |
|---|---|---|
| `Meziantou.Analyzer` | `3.0.44` | A Roslyn analyzer to enforce some good practices in C# in terms of design, usage, security, performance and style. |
| `Microsoft.CodeAnalysis.BannedApiAnalyzers` | `3.3.4` | A set of Roslyn analyzers that allow you to ban specific APIs from being used in your codebase, either by specifying them directly or by referencing an external configuration file. Useful for enforcing architectural boundaries, preventing usage of dangerous or deprecated APIs and maintaining a clean and consistent codebase. |

### Package References
| Package | Version | Description |
|---|---|---|
| `Microsoft.Sbom.Targets` | `4.1.5` | Provides MSBuild targets for SBOM generation. Declared as a build-time-only dependency (`PrivateAssets="all"`) so it does not flow to consumers of projects that use this SDK. |
