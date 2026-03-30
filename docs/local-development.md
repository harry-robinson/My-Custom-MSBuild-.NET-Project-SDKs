# 💻 Local Development
Because MSBuild SDK resolution happens at evaluation time, before the build even begins, you cannot reference an SDK project directly the way you might with a regular NuGet package reference. Instead, you need to pack the SDK into a NuGet package and point a local `NuGet.Config` at the output directory. This gives you a tight, fast feedback loop. Make a change, repack and the consuming project picks up the changes on the next build, without needing to publish to a remote feed.

Pack the SDK project:
```powershell
dotnet pack src/MyCustom.NET.Sdk/MyCustom.NET.Sdk.csproj --output /local/path/nupkgs-folder/
```

Alternatively, you can pack via **right-click → Pack** in Visual Studio's Solution Explorer or simply build the solution since `GeneratePackageOnBuild` is set to `true` in the SDK projects so a regular build will produce the `.nupkg` automatically.

Then configure a local package source in your `NuGet.Config`, pointing to the pack output directory:
```xml
<configuration>
  <packageSources>
    <add key="Local" value="/local/path/nupkgs-folder/" />
  </packageSources>
</configuration>
```

For more information on setting up local NuGet feeds, see [Local NuGet Feeds](https://learn.microsoft.com/en-us/nuget/hosting-packages/local-feeds) and the [NuGet.Config reference](https://learn.microsoft.com/en-us/nuget/reference/nuget-config-file).

Then update `global.json` to reference the version you packed. See [Referencing the SDK](../README.md#referencing-the-sdk) for details on version pinning via `global.json`.

> [!TIP]
> NuGet caches packages aggressively. When iterating locally, if your changes do not appear to be taking effect after repacking, the most likely cause is that NuGet is still using a previously cached version of the package. You have two options:
>
> **Option 1 - Flush the NuGet cache.** Clear the local NuGet cache to force NuGet to re-resolve the package from your local source on the next build.
>
> Via the command line:
> ```powershell
> dotnet nuget locals all --clear
> ```
>
> Via Visual Studio: **Tools → NuGet Package Manager → Package Manager Settings → Clear All NuGet Cache(s)**.
>
> **Option 2 - Bump the version number.** Increment the package version before repacking and update `global.json` to match, forcing NuGet to treat it as a new package entirely.
>
> It is also technically possible to delete the cached package files directly from the file system, but locating the right cache directory and ensuring all relevant files are removed can be fiddly and error-prone. Options 1 and 2 are simpler and more reliable in practice.

> [!TIP]
> For rapid iteration during development, you can bypass the NuGet resolver entirely by importing the SDK's `Sdk.props` and `Sdk.targets` files directly in your consuming project file, pointing straight at the source files on disk:
>
> ```xml
> <Project>
>   <Import Project="/local/path/to/Sdk/Sdk.props" />
>
>   <!-- Project content -->
>
>   <Import Project="/local/path/to/Sdk/Sdk.targets" />
> </Project>
> ```
>
> This skips the pack and NuGet resolution steps entirely; no local caches to flush and no version numbers to bump. Changes to the SDK are picked up immediately on the next build with no intermediate steps. That said, this is a quick and dirty approach that bypasses SDK resolution entirely and should only be used as a temporary convenience during active development and never committed as a permanent reference.
