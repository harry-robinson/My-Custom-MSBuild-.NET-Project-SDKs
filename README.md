# My Custom MSBuild .NET Project SDKs
A collection of custom-built MSBuild project SDKs for .NET (Core) and ASP.NET Core development, built primarily as an educational resource for anyone looking to understand how custom MSBuild project SDKs work — a topic with surprisingly sparse official documentation and few real-world examples to learn from.

The SDKs themselves are modern, opinionated and practical: sensible defaults with easy overrides, designed to be composed modularly so you can pick only what your project needs. Whether you adopt them directly, use them as a starting point for your own SDKs or simply read through them to understand the mechanics — the goal is to make this corner of the .NET ecosystem a little less mysterious.

| Using the SDKs | Authoring & Extending | Reference |
| --- | --- | --- |
| 👀 [At a Glance](#-at-a-glance) | 🏗️ [Authoring & Extending](docs/authoring-and-extending.md) | ⚖️ [The Case for Custom SDKs](docs/the-case-for-custom-sdks.md) |
| ✨ [Features](#-features) | 🛠️ [Local Development](docs/local-development.md) | 🔗 [Useful Resources](docs/useful-resources.md) |
| 🚫 [What's Not Included](#-whats-not-included) | 🐛 [Debugging](docs/debugging.md) | 📋 [Future Work](docs/future-work.md) |
| 🔧 [Prerequisites](#-prerequisites) | 🔄 [CI/CD Considerations](docs/ci-cd-considerations.md) | 🙏 [Acknowledgments](docs/acknowledgments.md) |
| ⚡ [Quick Start](#-quick-start) | | 🤔 [Musings](docs/musings.md) |
| 📦 [SDKs](#-sdks) | | |
| 🔌 [Usage](#-usage) | | |
| 🔢 [Versioning](#-versioning) | | |
| :warning: [Caveats](#warning-caveats) | | |

## 👀 At a Glance
| | |
|---|---|
| **Purpose** | Provide opinionated, organization-level defaults for .NET and ASP.NET Core projects using custom MSBuild SDKs |
| **Distribution** | NuGet packages resolved via MSBuild SDK resolution |
| **Primary SDKs** | `MyCustom.NET.Sdk`, `MyCustom.NET.Sdk.Web` |
| **Target Framework** | Written and tested against .NET 10 |
| **Override Support** | All defaults can be overridden in consuming projects |
| **Extensibility** | Designed to be extended or used as a foundation for custom SDKs |
| **Use Case** | Standardizing build configuration across multiple repositories and development teams |

These SDKs allow organizations to enforce consistent build conventions with a single line in a project file: `<Project Sdk="MyCustom.NET.Sdk">` or `<Project Sdk="MyCustom.NET.Sdk.Web">`.

## ✨ Features
These SDKs provide organization-level defaults for .NET projects such as:
* Modern compiler settings
* Consistent analyzer configuration
* Standardized build and packaging behavior
* Shared project conventions

## 🚫 What's Not Included
These SDKs deliberately stop at the MSBuild boundary. Several things that commonly accompany a well-configured .NET repository are intentionally out of scope, not because they cannot be distributed via an SDK, but because they are better suited to a `dotnet new` template that scaffolds a repository from scratch rather than something silently injected into every build.

| Item | Description |
|---|---|
| `.gitignore` | Tells Git which files and folders to exclude from source control. |
| `.gitattributes` | Configures Git's handling of line endings, diff behavior and merge strategies on a per-file basis. |
| `.editorconfig` | Defines code style and formatting rules enforced in the IDE and, with `EnforceCodeStyleInBuild=true`, at build time. |
| `global.json` | Pins the .NET SDK and MSBuild SDK versions for a repository, ensuring consistent tooling across all environments. |
| `Directory.Build.props` | A repository-level MSBuild file, evaluated before project files, for sharing common properties across all projects in a repository. |
| `Directory.Packages.props` | Centralizes NuGet package version management across all projects in a repository via Central Package Management. |
| `UseArtifactsOutput` | Consolidates build outputs from all projects into a common location, separated by project, simplifying tooling and CI artifact collection. |

The goal is to make the correct configuration the default configuration, while still allowing individual projects to override anything when necessary.

## 🔧 Prerequisites
| Requirement | Minimum Version | Notes |
|---|---|---|
| .NET SDK | 10.0 | Required to build and consume the SDKs. See [Caveats](#warning-caveats) for notes on using earlier versions of .NET. |
| MSBuild | 17.10 | Included with the .NET 10 SDK - no separate installation required. |
| IDE | VS 2022 (17.10), Rider 2024.1 or VS Code (any recent version) | An IDE is technically optional. VS Code requires the [C# Dev Kit](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit) extension. Earlier versions of Visual Studio and Rider have limited or no `net10.0` support. |

> [!NOTE]
> If you are only consuming the SDKs in an existing project, any IDE with .NET 10 support is sufficient. The MSBuild version requirement is satisfied automatically by installing the .NET 10 SDK and does not need to be managed separately.

## ⚡ Quick Start
Add the SDK to `global.json` at the root of your repository where x.x.x is the latest or desired version of the SDK (e.g., `1.0.0`):
```json
{
  "msbuild-sdks": {
    "MyCustom.NET.Sdk": "x.x.x",
    "MyCustom.NET.Sdk.Web": "x.x.x"
  }
}
```

Reference it in your `.csproj`, using either `MyCustom.NET.Sdk` for .NET projects or `MyCustom.NET.Sdk.Web` for ASP.NET Core projects:
```xml
<Project Sdk="MyCustom.NET.Sdk">

  <PropertyGroup>
    <!-- Your project properties here as normal -->
  </PropertyGroup>

</Project>
```

That's it. The SDK is now active and your project inherits all default properties and targets. See [SDKs](#-sdks) for an overview of available SDKs and [Usage](#-usage) for more details including all available ways to reference the SDK, how to override default properties and version pinning options.

## 📦 SDKs
This repository is a monorepo containing multiple custom MSBuild project SDKs, each versioned independently in its own folder under `src`. The SDKs form a hierarchy, where each layer extends the one beneath it as follows:

| SDK                                              | Description                   | Import / Evaluation Order                                                                                                |
| ------------------------------------------------ | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| [MyCustom.NET.Sdk](src/MyCustom.NET.Sdk)         | Base SDK for .NET projects    | 1. Microsoft.NET.Sdk (built-in)<br>2. MyCustom.NET.Sdk                                                                   |
| [MyCustom.NET.Sdk.Web](src/MyCustom.NET.Sdk.Web) | SDK for ASP.NET Core projects | 1. Microsoft.NET.Sdk (built-in)<br>2. Microsoft.NET.Sdk.Web (built-in)<br>3. MyCustom.NET.Sdk<br>4. MyCustom.NET.Sdk.Web |

> [!NOTE]
> The import and evaluation order shown above have been simplified for readability. In practice, Microsoft's .NET SDKs are highly complex, each importing numerous `.props` and `.targets` files, which themselves import further files and so on. The actual import chain runs far deeper than depicted, but the table is intended to convey the general structure of how these SDKs build upon one another.

Documentation for each SDK, including available properties and targets, can be found in the SDK's own `README.md` file and directly within the SDK source code via XML comments.

> [!NOTE]
> To understand why the import chain is structured the way it is, it helps to first understand how MSBuild resolves and loads SDKs. For further and official information, see [How project SDKs are resolved](https://learn.microsoft.com/en-us/visualstudio/msbuild/how-to-use-project-sdk?view=visualstudio#how-project-sdks-are-resolved).

When an MSBuild project declares an SDK (e.g. `<Project Sdk="Microsoft.NET.Sdk.Web">`), MSBuild resolves that SDK and implicitly injects two imports into the project, `Sdk.props` at the very top and `Sdk.targets` at the very bottom. When `Microsoft.NET.Sdk.Web`'s `Sdk.props` and `Sdk.targets` are evaluated, they in turn explicitly import `Microsoft.NET.Sdk`'s own `Sdk.props` and `Sdk.targets` first, before applying their own logic. This means that despite `Microsoft.NET.Sdk.Web` being the declared SDK and sitting at the top of the import chain, `Microsoft.NET.Sdk` is actually the first to be fully loaded. `Microsoft.NET.Sdk.Web` then layers its configuration on top of it afterward.

While the `MyCustom.NET.Sdk.Web` import chain may seem counterintuitive (i.e., one might expect `MyCustom.NET.Sdk` to follow `Microsoft.NET.Sdk` directly rather than `Microsoft.NET.Sdk.Web`), there is a good reason for this ordering. Since `Microsoft.NET.Sdk.Web` always imports `Microsoft.NET.Sdk` internally, injecting `MyCustom.NET.Sdk` between them is not only impossible, as we have no control over what `Microsoft.NET.Sdk.Web` imports, but would also effectively undermine Microsoft's established import chain, potentially causing unexpected behavior or breakage.

## 🔌 Usage
The SDKs are distributed as NuGet packages and can be consumed however NuGet packages typically are; via NuGet.org, a private NuGet feed or a local NuGet source for development purposes.

### Referencing the SDK
There are four main ways to reference an MSBuild project SDK in a project file.

**1 - Inline in the project file:**
```xml
<Project Sdk="MyCustom.NET.Sdk/x.x.x">
```

**2 - As an explicit SDK element inside the project file:**
```xml
<Project>
  <Sdk Name="MyCustom.NET.Sdk" Version="x.x.x" />
  <Sdk Name="MyOtherCustom.NET.Sdk" Version="x.x.x" />
</Project>
```

**3 - As a combination of both:**
```xml
<Project Sdk="MyCustom.NET.Sdk/x.x.x">
  <Sdk Name="MyCustom.NET.Sdk.Web" Version="x.x.x" />
</Project>
```

**4 - As a directive in a file-based app:**
.NET 10 introduces file-based apps, which run a single `.cs` file directly without a `.csproj`. The SDK is declared using a `#:sdk` directive at the top of the file:
```csharp
#:sdk MyCustom.NET.Sdk/x.x.x
```

As with project file references, if the version is pinned in `global.json`, it can be omitted:
```csharp
#:sdk MyCustom.NET.Sdk
```

> [!NOTE]
> The `name/version` shorthand (e.g. `<Project Sdk="MyCustom.NET.Sdk/x.x.x">`) is only supported by the `Sdk` attribute on the `<Project>` element or file based app `#:sdk` directive. The explicit `<Sdk>` element requires the name and version as separate `Name` and `Version` attributes. The `<Import>` element accepts an `Sdk` attribute for the SDK name but does not support inline version specification, relying instead on the version resolved from `global.json`.

Since the custom SDKs import `Microsoft.NET.Sdk` internally as part of their import chain, there is no need to declare it separately. In the case of `MyCustom.NET.Sdk.Web`, `Microsoft.NET.Sdk.Web` is also imported internally and similarly does not need to be declared separately. Declaring `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Web` anywhere in your `.csproj`, whether via the `<Project Sdk="...">` attribute, the explicit `<Sdk>` element or a combination of both, would result in the respective SDK being loaded twice, causing MSBuild to emit a warning. The SDKs take precautions against this by checking, as best as possible, which SDKs have already been loaded before attempting to import them, guarding against accidental double-loading.

> [!NOTE]
> Unlike the built-in .NET SDKs published by Microsoft, which are bundled with the .NET SDK and require no version pinning, `MyCustom.NET.Sdk` is distributed as a NuGet package and must have its version explicitly specified. The recommended way to do this is centrally in `global.json` rather than in each project file individually:
```json
{
  "msbuild-sdks": {
    "MyCustom.NET.Sdk": "x.x.x"
  }
}
```

When a version is specified in `global.json`, it can be omitted from the project file:
```xml
<Project Sdk="MyCustom.NET.Sdk">
```

Pinning the version in `global.json` has several advantages over specifying it inline in each project file:
* Consistency - All projects in the repository are guaranteed to reference the same version, eliminating the risk of different projects silently using different versions.
* Easier upgrades - Bumping the SDK version requires a change in one place rather than hunting down every project file.
* Cleaner project files - Project files stay focused on project-specific configuration rather than version management concerns.

> [!WARNING]
> Only one version of each SDK can be used during a build. If the same SDK is referenced at two different versions, for example once in `global.json` and once inline in a project file, MSBuild will emit a warning. If you are using `global.json`, do not also specify a version inline.

> [!NOTE]
> `global.json` is resolved by walking up the directory tree from the project file, similar to how `Directory.Build.props` is resolved. Placing it at the root of your repository ensures it applies to all projects within it. For further information on how `global.json` works and all available options, see the [global.json reference](https://learn.microsoft.com/en-us/dotnet/core/tools/global-json).

> [!NOTE]
> MSBuild supports several SDK resolution mechanisms. Custom NuGet-based SDKs, such as the ones in this repository, are resolved via the NuGet SDK resolver, which locates the SDK package from the configured NuGet sources using the version specified inline, via the explicit `<Sdk>` element or in `global.json`. Other resolution mechanisms exist, such as MSBuild's built-in resolver for the default .NET SDKs (e.g. `Microsoft.NET.Sdk`) which are resolved from the .NET SDK installation on the machine rather than from NuGet. For further information on referencing MSBuild project SDKs and the full version resolution order, see [How project SDKs are resolved](https://learn.microsoft.com/en-us/visualstudio/msbuild/how-to-use-project-sdk?view=visualstudio#how-project-sdks-are-resolved).

### Overriding Default Properties
All default properties set by the SDK can be overridden in your project file as you would with any standard MSBuild property. Simply redeclare the property in a `<PropertyGroup>` within your `.csproj`:
```xml
<Project Sdk="MyCustom.NET.Sdk">

  <PropertyGroup>
    <!-- Override an SDK default -->
    <Nullable>disable</Nullable>
  </PropertyGroup>

</Project>
```

## 🔢 Versioning
The SDKs in this repository are distributed as NuGet packages and follow [Semantic Versioning (SemVer) 2.0.0](https://semver.org). Version numbers follow the format: `MAJOR.MINOR.PATCH`

### Versioning Rules
| Change Type | Version Increment | Description |
|---|---|---|
| **Breaking changes** | **MAJOR** | Changes that may alter build behavior, modify default values, remove properties or targets or require changes in consuming projects. |
| **New features** | **MINOR** | Backwards-compatible additions such as new optional properties, targets, conventions or build functionality. |
| **Bug fixes** | **PATCH** | Backwards-compatible fixes, documentation improvements or internal changes that do not alter expected build behavior. |

Because MSBuild SDKs participate directly in project evaluation and build logic, even minor updates may introduce new defaults or conventions. It is therefore recommended to review release notes when upgrading between versions.

### Upgrading
When the SDK version is pinned in `global.json` (recommended), upgrading simply requires updating the version number in one place:
```json
{
  "msbuild-sdks": {
    "MyCustom.NET.Sdk": "x.x.x",
    "MyCustom.NET.Sdk.Web": "x.x.x"
  }
}
```

After updating the version, the new SDK will be resolved automatically on the next build. Centralizing the version in `global.json` ensures that all projects in the repository use the same SDK version and makes upgrades straightforward.

### SDK Version Alignment
Each SDK in this repository is published as its own NuGet package and is versioned independently. However, because some SDKs extend others (for example `MyCustom.NET.Sdk.Web` builds on top of `MyCustom.NET.Sdk`), releases are **typically** published with matching version numbers. Using matching versions is recommended to ensure compatibility across the SDK hierarchy.

While it is technically possible to mix versions, doing so may lead to unexpected behavior if a newer SDK relies on properties, targets or conventions introduced in a newer version of another SDK in the hierarchy.

### Compatibility Considerations
Since these SDKs influence MSBuild evaluation and build behavior, upgrading may change default property values or introduce additional build logic. When upgrading between versions it is recommended to:

* Review release notes
* Verify builds locally and in CI
* Inspect warnings or changes in build output
* Generate a binary log (`dotnet build -bl`) if troubleshooting is required
* Use the [MSBuild Structured Log Viewer](https://msbuildlog.com) when investigating build behavior changes after an upgrade

## :warning: Caveats
The SDKs in this repository are written and tested against .NET 10. Accounting for every property, target and default value change across all .NET versions is a significant undertaking and is outside the scope of this project. That said, these SDKs are not strictly limited to .NET 10 and may work with other versions, but behavior may differ. If you are targeting a different version of .NET, it is worth reviewing the SDK properties and targets carefully to ensure they are compatible with your target framework and that any default values align with your expectations.

It is also worth keeping in mind that not everything you find online is accurate in all contexts. A property's default value documented today may not reflect what it was in an earlier version of .NET and some properties that appear to be SDK defaults are actually set by Visual Studio project templates rather than the SDK itself. When in doubt, the source of truth is always the SDK source code, not a search result alone. For diagnostic tooling that can help verify actual resolved values and build behavior, see [Debugging](docs/debugging.md).
