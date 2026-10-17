# Bump Patch Version

Bump the patch version in `package.json` and open a pull request — a composite GitHub Action you can call from any Node repo.

[![GitHub release](https://img.shields.io/github/v/release/dytsou/bump-patch-version)](https://github.com/dytsou/bump-patch-version/releases)
[![CI](https://github.com/dytsou/bump-patch-version/actions/workflows/ci.yml/badge.svg)](https://github.com/dytsou/bump-patch-version/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

[English](README.md) · [繁體中文](README-zh.md)

## Table of contents

- [Quick Start](#quick-start)
- [Behavior](#behavior)
- [Action inputs](#action-inputs)
- [Outputs](#outputs)
- [Caller contract](#caller-contract)
- [Support](#support)
- [Contributing](#contributing)
- [Important Notes](#important-notes)

## Quick Start

1. **Checkout with history** — the Action does not checkout. Use `fetch-depth: 0` and `fetch-tags: true` so the tag-ahead check can see tags.
2. **Add a caller workflow** (manual bump is the usual trigger):

```yaml
# .github/workflows/bump-patch-version.yml
name: Bump patch version

on:
  workflow_dispatch:

permissions:
  contents: read

jobs:
  bumpPatchVersion:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
    steps:
      - uses: actions/checkout@v7
        with:
          fetch-depth: 0
          fetch-tags: true

      - uses: dytsou/bump-patch-version@v0.1.0
```

3. **Run the workflow** from the Actions tab — a PR opens with the bumped `package.json`.

### Basic Usage

Defaults are enough for a root `package.json` on `main`:

```yaml
- uses: dytsou/bump-patch-version@v0.1.0
```

### Pin by commit SHA (recommended)

```yaml
- uses: dytsou/bump-patch-version@2e33168f7243c56c99aab840c76f9b6771fc4292 # v0.1.0
```

### Example (custom path and labels)

```yaml
- uses: dytsou/bump-patch-version@v0.1.0
  with:
    package-json-path: packages/app/package.json
    base-branch: main
    labels: ci, maintenance
```

## Behavior

1. Fail if `base-branch` (default `main`) has **no commits** after the latest tag merged into that branch.
2. If there are no tags yet, continue.
3. Bump `package.json` patch (`1.2.3` → `1.2.4`) with Node — no pnpm/npm CLI.
4. Open a PR with the version change via `peter-evans/create-pull-request`.

## Action inputs

Composite Action: `dytsou/bump-patch-version@v0.1.0`


| Name | Required | Default | Description |
| :--- | :--- | :--- | :--- |
| `package-json-path` | No | `package.json` | Path relative to the workspace |
| `base-branch` | No | `main` | Tag-ahead check + PR base |
| `labels` | No | `ci, maintenance` | Comma-separated PR labels |
| `token` | No | `${{ github.token }}` | Token for `create-pull-request` |

## Outputs


| Name | Description |
| ---- | ----------- |
| `version` | New version string after the patch bump |
| `pull-request-url` | Created PR URL (empty if none) |

## Caller contract

### Prerequisites

1. A `package.json` with a valid `x.y.z` (optional leading `v`) `version` field
2. Workflow job permissions: `contents: write`, `pull-requests: write`
3. Checkout before calling the Action (`fetch-depth: 0`, `fetch-tags: true`)

### Local smoke (this repository)

```bash
node lib/bump-package-json.test.mjs
```

## Support

If something is not working:

- Confirm checkout used `fetch-depth: 0` and `fetch-tags: true`
- Confirm the job has `contents: write` and `pull-requests: write`
- Open an [issue](https://github.com/dytsou/bump-patch-version/issues) with the failing workflow run URL (redact secrets)

### When things go wrong


| What you see | Likely cause |
| ------------ | ------------ |
| `… has no commits after tag …` | Base branch is not ahead of the latest tag; merge or push first |
| `Invalid semver version` | `package.json` `version` is not `x.y.z` |
| `package.json not found` | Wrong `package-json-path` relative to the workspace |
| No PR opened | No file changes (bump did not run), or token lacks PR permissions |

## Contributing

Contributions welcome — see [CONTRIBUTING.md](CONTRIBUTING.md) and our [Code of Conduct](CODE_OF_CONDUCT.md). Star the repo, [open issues](https://github.com/dytsou/bump-patch-version/issues), or send PRs.

## Important Notes

> [!IMPORTANT]
> **This Action does not checkout.** Callers must checkout first with full history and tags.

> [!WARNING]
> The bump edits `package.json` only. It does **not** run npm `preversion` / `version` / `postversion` lifecycle scripts, and it does not update lockfiles.

## License

This project is open source and available under the [MIT License](LICENSE).
