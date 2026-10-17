# bump-patch-version

GitHub composite Action: bump the **patch** version in `package.json` and open a pull request.

**Caller must checkout first** with full history and tags (`fetch-depth: 0`, `fetch-tags: true`). This Action does not checkout.

Bump is done by editing `package.json` with Node — no pnpm/npm CLI, and no `preversion` / `version` / `postversion` lifecycle scripts.

## Quick start

```yaml
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

Pin by commit SHA in production (same style as other personal Actions):

```yaml
- uses: dytsou/bump-patch-version@<commit-sha> # v0.1.0
```

## Behavior

1. Fail if `base-branch` (default `main`) has **no commits** after the latest tag merged into that branch.
2. If there are no tags yet, continue.
3. Bump `package.json` patch (`1.2.3` → `1.2.4`).
4. Open a PR with the version change.

## Inputs

| Input | Default | Description |
|-------|---------|-------------|
| `package-json-path` | `package.json` | Path relative to workspace |
| `base-branch` | `main` | Tag-ahead check + PR base |
| `labels` | `ci, maintenance` | PR labels |
| `token` | `${{ github.token }}` | Token for `create-pull-request` |

## Outputs

| Output | Description |
|--------|-------------|
| `version` | New version string |
| `pull-request-url` | Created PR URL (empty if none) |

## Local check

```bash
node lib/bump-package-json.test.mjs
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Please follow the [Code of Conduct](CODE_OF_CONDUCT.md).
