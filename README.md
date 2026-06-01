# jcdcdev.Umbraco.GitHub.Release

This GitHub Action automates creating and publishing an Umbraco package release to NuGet. It checks for relevant changes since the last release tag, optionally skips releases when only ignored files changed, uploads build artifacts and pushes packages to NuGet. It also creates a GitHub Release using `jcdcdev/jcdcdev.GitHub.CreateRelease`.

Looking to build and package your Umbraco project first? Check out [🚧 jcdcdev.Umbraco.GitHub.Build](https://github.com/jcdcdev/jcdcdev.Umbraco.GitHub.Build).

#### **Usage Example**
```yaml
name: 🚀 Release
on:
	push:
		tags:
			- 'v*.*.*'
	workflow_dispatch:
jobs:
	release:
		runs-on: ubuntu-latest
		steps:
			- name: Checkout
				uses: actions/checkout@v5

			- name: Release
				uses: jcdcdev/jcdcdev.Umbraco.GitHub.Release@main
				with:
					artifact-name: MyUmbracoProject
					version: 1.0.0
					github-token: ${{ secrets.GITHUB_TOKEN }}
					nuget-api-key: ${{ secrets.NUGET_API_KEY }}
```

#### **Inputs**

| Input                    | Description                                                                                        | Required | Default                                                                        | Example                               |
| ------------------------ | -------------------------------------------------------------------------------------------------- | -------- | ------------------------------------------------------------------------------ | ------------------------------------- |
| `artifact-name`          | Artifact name to download and publish.                                                             | Yes      | None                                                                           | `MyUmbracoProject`                    |
| `version`                | Version string used for the release and NuGet push.                                                | Yes      | None                                                                           | `1.0.0`                               |
| `nuget-api-key`          | NuGet API key used to push packages.                                                               | No       | None                                                                           | `${{ secrets.NUGET_API_KEY }}`        |
| `github-token`           | GitHub token used to create releases and edit PRs.                                                 | Yes      | None                                                                           | `${{ secrets.GITHUB_TOKEN }}`         |
| `nuget-source`           | NuGet source URL to push packages to.                                                              | No       | `https://api.nuget.org/v3/index.json`                                          | `https://api.nuget.org/v3/index.json` |
| `nuget-skip-push`        | Skip pushing packages to NuGet when `true`.                                                        | No       | `false`                                                                        | `true`                                |
| `ignore-files`           | Comma-separated patterns; if only these files changed, the release is skipped. Supports wildcards. | No       | `SECURITY.md,.github/README.md,docs/**,.github/workflows/*.yml,GitVersion.yml` | `docs/**,.github/**`                  |
| `strict-umbraco-version` | Fail when the input major version doesn't match this action's Umbraco major.                       | No       | `true`                                                                         | `false`                               |

#### **Behavior / Notes**

- The action determines the last release tag merged into the current branch and computes changed files since that tag. If only ignored files changed, it sets `SKIP=true` and skips the release.
- When running on a pull request close event the action can update the PR title and labels to indicate whether the release was created or skipped.