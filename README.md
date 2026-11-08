# .github

Default [community health files](https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/creating-a-default-community-health-file) for all repositories under [`@wezzcoetzee`](https://github.com/wezzcoetzee).

## What's Included

| File | Purpose |
|------|---------|
| `CODE_OF_CONDUCT.md` | Contributor Covenant v2.1 |
| `CONTRIBUTING.md` | Contribution guidelines |
| `SECURITY.md` | Vulnerability disclosure policy |
| `SUPPORT.md` | How to get help |
| `LICENSE` | MIT License |
| `.github/PULL_REQUEST_TEMPLATE.md` | Default PR template |
| `.github/ISSUE_TEMPLATE/` | Bug report and feature request forms |
| `.github/CODEOWNERS` | Default code owners |
| `.github/dependabot.yml` | Automated dependency updates |
| `.github/workflows/` | Local automation and a reusable macOS release workflow |

GitHub uses the community health files, issue/PR templates and funding configuration as defaults for repositories that don't define their own. Workflows, CODEOWNERS, Dependabot configuration and the license are not inherited. The PR linting, auto-labeling and stale workflows only run in this repository.

## macOS releases

Add `.github/workflows/release.yml` to an app repository to build and publish a GitHub Release when you push a version tag:

```yaml
name: Release

on:
  push:
    tags: ["v*"]

jobs:
  release:
    permissions:
      contents: write
    uses: wezzcoetzee/.github/.github/workflows/macos-release.yml@main
```

The reusable workflow checks out the caller's tag on `macos-26`, sets `VERSION` to the tag without its leading `v`, and runs `./make-app.sh "$VERSION"`. It publishes `dist/*.dmg` and `dist/*.zip` with generated release notes. Each artifact pattern must match a file, and filenames must be unique. Rerunning a tag replaces matching assets on its existing release without changing its notes or prerelease status. The workflow rejects branch runs.

The app's build script owns packaging and signing. Notchivity's script already ad-hoc signs its app before packaging, so it needs no Apple credentials. Ad-hoc signing does not notarise the app or remove Gatekeeper's first-launch checks.

| Input | Default | Purpose |
|---|---|---|
| `runner` | `macos-26` | Choose a macOS runner with the app's required Xcode/Swift version. |
| `setup-command` | Empty | Install tools or select an installed Xcode before building. |
| `build-command` | `./make-app.sh "$VERSION"` | Build, sign and package using the app's own script. |
| `artifacts` | `dist/*.dmg` and `dist/*.zip` | Newline-separated file globs relative to the repository root. |
| `prerelease` | `false` | Mark a newly created release as a prerelease. |

For an XcodeGen app that only produces a DMG, override the defaults on the calling job:

```yaml
    with:
      setup-command: brew install xcodegen
      build-command: ./scripts/build-dmg.sh
      artifacts: dist/*.dmg
```

Build and setup commands run from the repository root and receive `VERSION` in their environment. Scripts must use it if the app version should match the tag. Callers can pin the reusable workflow to a commit SHA instead of tracking `main`. No `secrets: inherit` is needed for ad-hoc builds; the caller's `contents: write` permission allows release publishing.
