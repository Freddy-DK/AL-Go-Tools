# DeliverToNuGet

## Description

`DeliverToNuGet.ps1` is a custom AL-Go for GitHub delivery script, which replaces the built-in NuGet delivery.
For every app and test app in each project, it calculates the NuGet package name and checks whether the app has already been delivered before pushing anything.
If a package with the same version already exists on the feed, or the latest package on the feed contains an app with the same code, the app is skipped, otherwise a new package is created and pushed to the feed.

> [!NOTE]
> When comparing code, the script ignores the version number in app.json and system files that differ between builds of the same source code.

Continuous delivery (CD) packages are published with the `preview` prerelease tag, releases are published without a prerelease tag.
Packages are only delivered once per run, even if multiple projects contain the same app.

A delivery manifest (`DeliveryManifest-NuGet.json`) listing the packages pushed in the run is written to the temp folder.

## How to use

Place `DeliverToNuGet.ps1` in the `.github` folder of either:

- your **indirect template repository** - the script will be propagated to all repositories using the template when running *Update AL-Go System Files*, or
- **directly in the repository** where you want to use it.

Delivery requires a secret called `NuGetContext`, formatted as compressed Json, containing `serverUrl` and `token` as a minimum:

```json
{"serverUrl":"https://nuget.pkg.github.com/<owner>/index.json","token":"<token>"}
```

The token is the API key for the NuGet feed.

## Additional

The same behavior can be used for GitHub Packages by renaming the script to `DeliverToGitHubPackages.ps1`. The delivery target is derived from the file name, so the script will then use the `GitHubPackagesContext` secret and write the manifest to `DeliveryManifest-GitHubPackages.json`.
