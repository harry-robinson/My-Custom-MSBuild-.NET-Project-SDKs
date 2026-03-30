# 🏗️ Authoring & Extending
These SDKs are designed to be extended. Whether you want to add a few custom properties or targets on top of an existing SDK or build an entirely new SDK from scratch, this section covers what you need to know.

Much of the rationale and extensibility design is documented directly in the source code via XML comments. Browsing the `.props` and `.targets` files is encouraged if your goal is to understand how everything works under the hood or extend the existing SDKs with additional properties and targets.

## Solution Structure
```
My Custom MSBuild .NET Project SDKs/
├─ assets/                                # Non-code assets such as icons, images, etc.
├─ docs/                                  # Documentation, including this file
├─ Solution Items/                        # Solution-level files e.g. README.md, LICENSE, etc.
└─ src/                                   # All SDK source code
   ├─ MyCustom.NET.Sdk/                   # Base SDK for .NET projects
   │  └─ Sdk/                             # Required SDK folder, resolved by MSBuild
   │     ├─ Sdk.props                     # Imported at the top of the project file
   │     └─ Sdk.targets                   # Imported at the bottom of the project file
   ├─ MyCustom.NET.Sdk.Web/               # SDK for ASP.NET Core projects
   │  └─ Sdk/                             # Required SDK folder, resolved by MSBuild
   │     ├─ Sdk.props                     # Imported at the top of the project file
   │     └─ Sdk.targets                   # Imported at the bottom of the project file
   ├─ Directory.Build.props               # Shared NuGet package metadata across all SDKs
   └─ Directory.Build.targets             # Shared NuGet pack validation logic across all SDKs
```

> [!NOTE]
> `Directory.Build.props` and `Directory.Build.targets` at the `src` level are not part of any SDK. They define common NuGet package metadata (e.g. authors, license, repository URL, etc.) and shared build logic across all SDK projects, ensuring consistency without duplicating it in every `.csproj` file.

## SDK Structure
An MSBuild project SDK is a NuGet package with a specific folder structure. At its core, an SDK must contain a folder exactly named `Sdk` at the root of the package, containing two files: `Sdk.props` and `Sdk.targets`. This folder and file naming is a hard requirement enforced by MSBuild. It is not configurable.
```
MyCustom.NET.Sdk/
└─ Sdk/                    # Must be named exactly 'Sdk'. MSBuild will not resolve any other name
   ├─ Sdk.props            # Injected at the very top of the consuming project file
   └─ Sdk.targets          # Injected at the very bottom of the consuming project file
```

> [!WARNING]
> The `Sdk` folder name and the `Sdk.props` and `Sdk.targets` file names are **case-sensitive** on case-sensitive file systems such as Linux. `sdk/`, `SDK/`, `sdk.props` and `sdk.targets` will not be resolved by MSBuild. Ensure the casing is exact, regardless of the operating system you are developing on, to avoid resolution failures in CI or cross-platform environments.

## Sdk.props and Sdk.targets
When a project declares an SDK, MSBuild implicitly injects two imports: `Sdk.props` at the very top of the project file and `Sdk.targets` at the very bottom. This split is intentional and the distinction matters.
* **`Sdk.props`** is evaluated before any project content. It is the right place for properties and item definitions that need to be in place before the project file is read, such as default property values, conditional configuration and anything that the project file itself should be able to override.
* **`Sdk.targets`** is evaluated after all project content. It is the right place for targets, build logic and any properties or items that should be set after the project file has been fully read, ensuring they can take into account anything the project file has defined.

> [!IMPORTANT]
> As a general rule: **defaults and configuration go in `Sdk.props`, build logic and targets go in `Sdk.targets`**.

## Importing a Parent SDK
To build on top of `MyCustom.NET.Sdk`, import it explicitly at the top of both your `Sdk.props` and `Sdk.targets`. This ensures the parent SDK's properties and targets are loaded first, giving you a clean foundation to layer your own configuration on top of.

**`Sdk.props`**
```xml
<Project>

  <!-- Import the parent SDK props first, before any of your own configuration -->
  <Import Project="Sdk.props" Sdk="MyCustom.NET.Sdk" />

  <!-- Your custom default properties and item definitions go here -->

</Project>
```

**`Sdk.targets`**
```xml
<Project>

  <!-- Import the parent SDK targets first, before any of your own targets -->
  <Import Project="Sdk.targets" Sdk="MyCustom.NET.Sdk" />

  <!-- Your custom targets and build logic go here -->

</Project>
```

The consuming project then only needs to declare your SDK:
```xml
<Project Sdk="YourCustom.NET.Sdk">

  <PropertyGroup>
    
    <!-- Project-specific properties go here. Any properties set here will override defaults from both your SDK and the parent SDK. -->

  </PropertyGroup>

</Project>
```

`MyCustom.NET.Sdk` (and by extension `Microsoft.NET.Sdk`) is imported automatically as part of the chain. There is no need to declare them separately in the project file.

## Guarding Against Double Imports
When Microsoft authored `Microsoft.NET.Sdk`, they introduced a property called `UsingMicrosoftNETSdk` as a sentinel, a boolean flag set to `true` in `Sdk.props` that signals to anything else in the build that this SDK has already been loaded. Any SDK that depends on it can check for this property and skip the import if it is already present, avoiding the double-import warning MSBuild emits when the same SDK is loaded more than once.

The SDKs in this repository follow this same pattern. Each SDK declares its own sentinel in `Sdk.props` and `Sdk.targets` and guards any parent SDK imports using a `Condition`:
```xml
<Import Project="Sdk.props" Sdk="MyCustom.NET.Sdk"
        Condition="'$(UsingMyCustomNETSdk)' != 'true'" />
```

If you are building your own SDK on top of this one, declare your own sentinel property and apply the same guard to any imports. It is a simple convention but an important one; without it, double-import warnings surface quickly as the SDK hierarchy grows.

## Enforcing Non-Negotiable Standards
One of the advantages of a custom SDK over a traditional NuGet package is that it is evaluated as part of the MSBuild project model, which means it can inspect what a consuming project has set and react to it. This makes it possible to enforce non-negotiable standards directly in the SDK, rather than relying on code review or convention.

MSBuild provides three practical mechanisms for this.

### Emitting Warnings
To warn without failing the build, use the `Warning` task inside a target. This is appropriate for standards that are strongly recommended, but not strictly required or where you want to give teams time to comply before tightening enforcement:
```xml
<Target Name="WarnIfNullableDisabled" AfterTargets="Build">
  <Warning Condition="'$(Nullable)' != 'enable'"
           Text="Nullable reference types should be enabled. Set <Nullable>enable</Nullable> in your project file." />
</Target>
```

### Failing the Build
To fail the build outright, use the `Error` task instead. This is appropriate for standards that are non-negotiable and where a non-compliant build should never succeed:
```xml
<Target Name="EnforceNullable" BeforeTargets="Build">
  <Error Condition="'$(Nullable)' != 'enable'"
         Text="Nullable reference types must be enabled. Set <Nullable>enable</Nullable> in your project file." />
</Target>
```

> [!TIP]
> For `Warning` targets, prefer `AfterTargets="Build"` so the build completes and all warnings surface together. For `Error` targets enforcing non-negotiable standards, prefer `BeforeTargets="Build"` since the property value is known before compilation starts, there is no benefit to letting the build run only to fail it afterward.

### Preventing Overrides
To lock a property entirely and prevent consuming projects from overriding it, set it unconditionally in `Sdk.targets` rather than `Sdk.props`. Because `Sdk.targets` is injected at the very bottom of the project file, it is evaluated after all project content, meaning any value the project sets will be overwritten:
```xml
<!-- In Sdk.targets. Evaluated after the project file, overwriting anything the project set -->
<PropertyGroup>
  <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
</PropertyGroup>
```

This is the strongest form of enforcement and should be used sparingly. A consuming project has no way to override a property set unconditionally in `Sdk.targets` and no indication that their override is being silently ignored.

> [!WARNING]
> Silently overriding a property a developer has explicitly set in their project file is likely to cause confusion. If you lock a property this way, document it clearly and consider pairing it with a `Warning` or `Error` that fires if the project attempts to set a conflicting value, so the behavior is explicit rather than silent.

## Troubleshooting
For tips on diagnosing and troubleshooting MSBuild execution, including how to query resolved property values, generate binary logs and inspect the fully expanded project file, see the [Debugging](debugging.md) guide.
