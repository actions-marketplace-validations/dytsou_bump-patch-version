# Contributing to [bump-patch-version](https://github.com/dytsou/bump-patch-version)

We love your input! We want to make contributing to this project as easy and transparent as possible, whether it's:

- Reporting [an issue](https://github.com/dytsou/bump-patch-version/issues/new).
- Discussing the current state of the code.
- Submitting [a fix](https://github.com/dytsou/bump-patch-version/compare).
- Proposing [new features](https://github.com/dytsou/bump-patch-version/issues/new).
- Becoming a maintainer.

Please also read our [Code of Conduct](CODE_OF_CONDUCT.md).

## All Changes Happen Through Pull Requests

Pull requests are the best way to propose changes. We actively welcome your pull requests:

1. Fork the repo and create your branch from `main`.
2. If you've added code that should be tested, extend `lib/bump-package-json.test.mjs` (or add a focused check).
3. If you've changed Action inputs, outputs, or caller requirements, update `README.md`.
4. Run the local checks (see [Local development](#local-development)).
5. Open that pull request!

## Under the hood

This repository is a **composite GitHub Action** that:

1. Ensures the base branch has commits after the latest merged tag (skips when there are no tags).
2. Bumps the patch version in `package.json` with Node (no package-manager CLI).
3. Opens a pull request via `peter-evans/create-pull-request`.

Primary entry for consumers:

`dytsou/bump-patch-version@v0` (prefer pinning the commit SHA in production)

Layout:

| Path | Role |
| ---- | ---- |
| `action.yml` | Composite Action entrypoint |
| `lib/ensure-commits-after-tag.sh` | Tag-ahead guard |
| `lib/bump-package-json.mjs` | Patch bump + `GITHUB_OUTPUT` |
| `lib/bump-package-json.test.mjs` | Assert-based self-check |

## Local development

1. Fork the repository and clone it to your machine.
2. From the repository root, run:

```bash
node lib/bump-package-json.test.mjs
```

> [!NOTE]
> End-to-end PR creation needs a real GitHub repository and tokens; that path is exercised when a consumer runs the Action.

## Any contributions you make will be under the MIT Software License

In short, when you submit changes, your submissions are understood to be under the same [MIT License](https://choosealicense.com/licenses/mit/) that covers the project. Feel free to contact the maintainers if that's a concern.

## Report issues/bugs using GitHub's [issues](https://github.com/dytsou/bump-patch-version/issues)

We use GitHub issues to track public bugs. Report a bug by [opening a new issue](https://github.com/dytsou/bump-patch-version/issues/new); it's that easy!

### Bug reports

**Great bug reports** tend to have:

- A quick summary and/or background
- Steps to reproduce
  - Be specific!
  - Share the consumer workflow YAML (redact secrets)
  - Link the failing Actions run, if possible
- What actually happens
- What you expected would happen
- Notes (including Node version, and anything you already tried)

### Feature requests

**Great feature requests** tend to have:

- A quick idea summary
- What & why you want to add the specific feature
- Additional context such as sample `package.json` snippets or related Actions
