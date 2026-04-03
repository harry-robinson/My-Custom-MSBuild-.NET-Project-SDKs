# 🔄 CI/CD Considerations
There is nothing CI/CD-specific about consuming these SDKs, but there are a few things worth keeping in mind when running builds in a pipeline.

## NuGet Configuration
**[Pin your versions in `global.json`](https://learn.microsoft.com/en-us/dotnet/core/tools/global-json).** CI environments should produce deterministic, reproducible builds. Pinning both the .NET SDK version and the MSBuild SDK versions in `global.json` ensures that every build, whether local or in a pipeline, uses exactly the same tooling:
```json
{
  "sdk": {
    "version": "x.x.xxx"
  },
  "msbuild-sdks": {
    "MyCustom.NET.Sdk": "x.x.x"
  }
}
```

**[Configure your NuGet source](https://learn.microsoft.com/en-us/nuget/reference/nuget-config-file).** If you are distributing the SDKs via a private NuGet feed rather than NuGet.org, ensure your CI environment is configured to authenticate against that feed. Most CI platforms support this via environment variables or a `NuGet.Config` checked into the repository. Avoid hardcoding credentials; use secrets or service connections provided by your CI platform instead.

**If using a local NuGet source**, for example during SDK development in a branch pipeline, ensure the packed `.nupkg` files are available to the build agent and that the `NuGet.Config` path resolves correctly in the agent's working directory. Relative paths are generally more portable than absolute paths in this context.

> [!TIP]
> Because MSBuild SDK resolution happens before the build begins, a missing or misconfigured NuGet source will cause the build to fail at evaluation time with a resolver error, before any targets have run. If a pipeline fails immediately with an SDK resolution error, the NuGet source configuration and `global.json` file are the first places to look.

## CI vs Non-CI Builds
Some build behaviors that are expensive or unnecessary during local development, such as XML documentation generation or code style enforcement, are worth enabling in CI to catch issues early and produce complete build artifacts. The conventional pattern for toggling these is to condition them on the `ContinuousIntegrationBuild` property. When `true`, this property also signals to the .NET SDK itself to apply certain CI-appropriate settings, such as normalizing embedded source file paths for reproducible builds.

For example, to enable XML documentation generation only in CI:
```xml
<PropertyGroup Condition="'$(ContinuousIntegrationBuild)' == 'true'">
  <GenerateDocumentationFile>true</GenerateDocumentationFile>
</PropertyGroup>
```

The same pattern applies to `EnforceCodeStyleInBuild` or any other property you want to gate on a CI context.

`ContinuousIntegrationBuild` is not set automatically. It is your responsibility to set it based on an environment variable provided by your CI platform. For GitHub Actions:
```xml
<PropertyGroup Condition="'$(GITHUB_ACTIONS)' == 'true'">
  <ContinuousIntegrationBuild>true</ContinuousIntegrationBuild>
</PropertyGroup>
```

This can live in a `Directory.Build.props` file at the root of your repository so it applies to all projects without repeating it in each project file or it can be incorporated directly into your own custom MSBuild project SDK. For further reading, see the [`ContinuousIntegrationBuild` MSBuild property reference](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/msbuild-props#continuousintegrationbuild).
