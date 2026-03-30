# ⚖️ The Case for Custom SDKs
As an engineering manager overseeing multiple software development teams spread across different countries and varying skillset levels, one of the most persistent challenges is consistency. How do you ensure that a team in one country is building, testing and publishing their .NET projects the same way as a team in another, without micromanaging, without sprawling wiki pages that nobody reads and without relying on every developer having the same depth of MSBuild or best practices knowledge?

The answer, at least in part, is to make the right thing the default thing.

`Directory.Build.props` is a great tool and often the first place teams reach for when they want to share build configuration. For a single repository or solution it works well, but it does not scale across repositories. MSBuild discovers `Directory.Build.props` by walking up the directory tree, meaning its reach stops at the repository boundary at best. In a large distributed engineering organization with many repositories, you're back to copying files and copying files means drift, inconsistency and a maintenance burden that grows with every new team and every new repository.

Traditional NuGet packages with bundled `.props` and `.targets` files are another option and a step in the right direction, but they come with their own friction. They require explicit package references in every project, they can be overridden or forgotten and they do not benefit from the first-class SDK resolution and layering that MSBuild's SDK system provides. They are also essentially static. A traditional NuGet package cannot inspect the project consuming it and react to it. A custom SDK, by contrast, is evaluated as part of the MSBuild project model, meaning it can read project properties, conditions and item groups and make dynamic decisions accordingly. For example, a custom SDK can detect the target framework, the output type or whether certain packages are already referenced and conditionally apply properties, inject package references or enable targets on the fly; something a traditional NuGet package simply cannot do in any clean or maintainable way. This also means that if a particular standard is non-negotiable, a custom SDK can enforce it directly using MSBuild conditions, emitting a warning or failing the build outright if a consuming project deviates from it, something that is simply not possible with a file copy or a traditional NuGet package.

Custom MSBuild project SDKs offer something better. A single line in a `.csproj` file, e.g. `<Project Sdk="MyCustom.NET.Sdk.Web">` and the project inherits your organization's entire build standard. Compiler settings, analyzer rules, NuGet package conventions, publishing targets; all of it, applied automatically, versioned through NuGet and easy to update across every team and every repository at once. Junior developers do not need to know how MSBuild works. Senior developers are not burdened with policing configuration in code reviews. The standards are simply there, by default and teams can focus on writing code.

The following table summarizes how the three approaches compare:

| | `Directory.Build.props` | NuGet Package (`.props`/`.targets`) | Custom MSBuild SDK |
| --- | --- | --- | --- |
| **Cross-repository** | No | Yes | Yes |
| **Distribution** | File copy | NuGet feed | NuGet feed |
| **Versioning** | None | NuGet versioning | NuGet versioning |
| **Project file overhead** | None | `<PackageReference>` in every project, version optionally centralized via `Directory.Packages.props` | Single `Sdk=` attribute |
| **Can be overridden** | Yes | Yes | Yes |
| **Evaluated as part of MSBuild project model** | Yes | Partially | Yes |
| **Can react to project properties dynamically** | Yes | Limited | Yes |
| **Risk of being forgotten or removed** | Low | Medium | Low |

That is the motivation behind this work. It is not the right tool for every situation. For a single project or a small monorepo, it is almost certainly overkill. But for a large, distributed engineering organization that needs consistent, enforceable, low-friction build standards across many teams and many repositories, a custom MSBuild project SDK is one of the most powerful and underutilized tools available in the .NET ecosystem.
