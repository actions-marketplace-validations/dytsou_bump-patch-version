# Bump Patch Version

把 `package.json` 的 **patch** 版本加一，並開出 pull request — 可從任何 Node repo 呼叫的 composite GitHub Action。

[![GitHub release](https://img.shields.io/github/v/release/dytsou/bump-patch-version)](https://github.com/dytsou/bump-patch-version/releases)
[![CI](https://github.com/dytsou/bump-patch-version/actions/workflows/ci.yml/badge.svg)](https://github.com/dytsou/bump-patch-version/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

[English](README.md) · [繁體中文](README-zh.md)

## 目錄

- [快速開始](#快速開始)
- [行為](#行為)
- [Action 輸入](#action-輸入)
- [輸出](#輸出)
- [Caller 契約](#caller-契約)
- [支援](#支援)
- [貢獻](#貢獻)
- [重要說明](#重要說明)

## 快速開始

1. **先 checkout 完整歷史** — 本 Action 不會 checkout。請使用 `fetch-depth: 0` 與 `fetch-tags: true`，tag-ahead 檢查才看得到 tag。
2. **加入 caller workflow**（通常用手動觸發）：

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

      - uses: dytsou/bump-patch-version@v1
```

3. **到 Actions 分頁跑 workflow** — 會開一支含 bumped `package.json` 的 PR。

### 基本用法

根目錄 `package.json`、base 為 `main` 時，用預設即可：

```yaml
- uses: dytsou/bump-patch-version@v1
```

Major tag（`@v1`）會跟著同 major 浮動；固定版本請用 `@v1.0.0`。

### 建議用 commit SHA pin

```yaml
- uses: dytsou/bump-patch-version@<commit-sha> # v1.0.0
```

### 範例（自訂路徑與 labels）

```yaml
- uses: dytsou/bump-patch-version@v1
  with:
    package-json-path: packages/app/package.json
    base-branch: main
    labels: ci, maintenance
```

## 行為

1. 若 `base-branch`（預設 `main`）在最新 merged tag 之後**沒有任何 commit**，則失敗。
2. 若尚無任何 tag，則繼續。
3. 用 Node 把 `package.json` patch 加一（`1.2.3` → `1.2.4`）— 不呼叫 pnpm/npm CLI。
4. 透過 `peter-evans/create-pull-request` 開 PR。

## Action 輸入

Composite Action：`dytsou/bump-patch-version@v1`


| 名稱 | 必填 | 預設 | 說明 |
| :--- | :--- | :--- | :--- |
| `package-json-path` | 否 | `package.json` | 相對於 workspace 的路徑 |
| `base-branch` | 否 | `main` | Tag-ahead 檢查與 PR base |
| `labels` | 否 | `ci, maintenance` | 逗號分隔的 PR labels |
| `token` | 否 | `${{ github.token }}` | 給 `create-pull-request` 用的 token |

## 輸出


| 名稱 | 說明 |
| ---- | ---- |
| `version` | bump 後的版本字串 |
| `pull-request-url` | 建立的 PR URL（沒有則為空） |

## Caller 契約

### 前置條件

1. `package.json` 需有合法的 `x.y.z`（可選前綴 `v`）`version` 欄位
2. Job permissions：`contents: write`、`pull-requests: write`
3. 呼叫 Action 前先 checkout（`fetch-depth: 0`、`fetch-tags: true`）

### 本倉庫本地檢查

```bash
node lib/bump-package-json.test.mjs
```

## 支援

若行為不如預期：

- 確認 checkout 有使用 `fetch-depth: 0` 與 `fetch-tags: true`
- 確認 job 有 `contents: write` 與 `pull-requests: write`
- 開 [issue](https://github.com/dytsou/bump-patch-version/issues) 並附上失敗的 workflow run URL（請遮罩 secrets）

### 常見狀況


| 你看到的現象 | 可能原因 |
| ------------ | -------- |
| `… has no commits after tag …` | Base branch 沒有超前最新 tag；請先 merge 或 push |
| `Invalid semver version` | `package.json` 的 `version` 不是 `x.y.z` |
| `package.json not found` | `package-json-path` 相對於 workspace 不正確 |
| 沒有開出 PR | 沒有檔案變更（bump 未執行），或 token 缺少 PR 權限 |

## 貢獻

歡迎貢獻 — 見 [CONTRIBUTING.md](CONTRIBUTING.md) 與 [Code of Conduct](CODE_OF_CONDUCT.md)。歡迎 star、[開 issue](https://github.com/dytsou/bump-patch-version/issues)，或送 PR。

## 重要說明

> [!IMPORTANT]
> **本 Action 不會 checkout。** Caller 必須先以完整歷史與 tags checkout。

> [!WARNING]
> Bump 只改 `package.json`。**不會**執行 npm 的 `preversion` / `version` / `postversion` lifecycle scripts，也不會更新 lockfile。

## License

本專案以 [MIT License](LICENSE) 開源。
