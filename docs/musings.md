# 🤔 Musings
[Phil Karlton](https://martinfowler.com/bliki/TwoHardThings.html) famously noted that naming things is one of the two hardest problems in computer science. Naming this repository and the SDKs within it proved no exception. 

I settled on the "My" prefix not out of a lack of imagination, but out of respect for the consumer's environment. In MSBuild, the name is the API. Calling these anything more than "My" would be presumptuous and potentially dangerous if it collided with your internal naming conventions.

When you fork this, the first thing you should do is rename the packages. A clear naming convention helps developers understand exactly what standards are being applied just by looking at the `<Project Sdk="...">` attribute. 

Avoid naming your SDKs in a way that collides with Microsoft's own SDK names such as `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Web`. MSBuild's built-in resolver handles these specific names and a handful of others by resolving them from the .NET SDK installation on the machine rather than from NuGet. A custom SDK with a colliding name is likely to either fail to resolve or resolve to the wrong package entirely, producing errors that can be difficult to diagnose.

Fork it, brand it and make it yours.
