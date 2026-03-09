# GitHub Actions ワークフロー設定ガイド

このドキュメントでは、`.github/workflows` ディレクトリに含まれる GitHub Actions ワークフローの設定について説明します。

## ワークフロー一覧

| ファイル名 | 説明 |
|---|---|
| `claude.yml` | Claude Code による Issue・PR コメント応答 |
| `claude-code-review.yml` | Claude Code による自動コードレビュー |

---

## 1. claude.yml — Claude Code インタラクション

**ファイルパス:** `.github/workflows/claude.yml`

### 概要

Issue やプルリクエストのコメントに `@claude` とメンションすることで、Claude が自動的に応答・作業を行うワークフローです。

### トリガー条件

以下のイベントが発生したときにワークフローが起動します。

| イベント | 条件 |
|---|---|
| `issue_comment` (created) | コメント本文に `@claude` が含まれる場合 |
| `pull_request_review_comment` (created) | レビューコメントに `@claude` が含まれる場合 |
| `pull_request_review` (submitted) | レビュー本文に `@claude` が含まれる場合 |
| `issues` (opened, assigned) | Issue 本文またはタイトルに `@claude` が含まれる場合 |

### パーミッション

```yaml
permissions:
  contents: read
  pull-requests: read
  issues: read
  id-token: write
  actions: read
```

> `actions: read` は、Claude が PR の CI 結果を参照するために必要です。

### 必要なシークレット

| シークレット名 | 説明 |
|---|---|
| `CLAUDE_CODE_OAUTH_TOKEN` | Claude Code の認証トークン |

### セットアップ手順

1. [Claude Code](https://claude.ai/code) にサインインし、OAuth トークンを取得します。
2. リポジトリの **Settings → Secrets and variables → Actions** を開きます。
3. `CLAUDE_CODE_OAUTH_TOKEN` という名前で取得したトークンを登録します。

### 使い方

Issue またはプルリクエストのコメントで `@claude` とメンションするだけです。

```
@claude このバグを修正してください
@claude コードをレビューしてください
```

### カスタマイズ

`claude.yml` 内のコメントアウトされたオプションを有効にすることで動作をカスタマイズできます。

```yaml
# カスタムプロンプトを指定する例
prompt: 'プルリクエストの説明を変更の概要で更新してください。'

# 使用できるツールを制限する例
claude_args: '--allowed-tools Bash(gh pr:*)'
```

---

## 2. claude-code-review.yml — 自動コードレビュー

**ファイルパス:** `.github/workflows/claude-code-review.yml`

### 概要

プルリクエストが作成・更新されたときに、Claude が自動的にコードレビューを行うワークフローです。

### トリガー条件

以下のプルリクエストイベントで起動します。

| イベント | 説明 |
|---|---|
| `opened` | PR が新規作成されたとき |
| `synchronize` | PR に新しいコミットが追加されたとき |
| `ready_for_review` | ドラフト PR がレビュー可能になったとき |
| `reopened` | クローズされた PR が再オープンされたとき |

### パーミッション

```yaml
permissions:
  contents: read
  pull-requests: read
  issues: read
  id-token: write
```

### 必要なシークレット

| シークレット名 | 説明 |
|---|---|
| `CLAUDE_CODE_OAUTH_TOKEN` | Claude Code の認証トークン |

### 使い方

設定後は、プルリクエストを作成するだけで自動的にレビューが実行されます。手動操作は不要です。

### カスタマイズ

#### 特定の作成者のみレビューする

特定のユーザーのみを対象にする場合、`if` 条件のコメントアウトを解除します。

```yaml
jobs:
  claude-review:
    if: |
      github.event.pull_request.user.login == 'external-contributor' ||
      github.event.pull_request.author_association == 'FIRST_TIME_CONTRIBUTOR'
```

#### 特定のファイル変更のみを対象にする

特定のファイルパターンが変更されたときのみ実行する場合、`paths` を設定します。

```yaml
on:
  pull_request:
    paths:
      - "src/**/*.ts"
      - "src/**/*.tsx"
```

---

## よくある質問

### Q. `@claude` とメンションしたのに反応しない

以下を確認してください。

- `CLAUDE_CODE_OAUTH_TOKEN` シークレットが正しく設定されているか
- GitHub Actions が有効になっているか（**Settings → Actions → General**）
- ワークフローファイルのシンタックスエラーがないか

### Q. 自動コードレビューを無効にしたい

`claude-code-review.yml` のトリガーイベントを削除するか、ファイル自体を削除してください。

### Q. Claude に特定の作業をさせたくない

`claude_args` オプションで使用できるツールを制限できます。

```yaml
claude_args: '--allowed-tools Bash(gh pr:*)'
```

---

## 参考リンク

- [claude-code-action ドキュメント](https://github.com/anthropics/claude-code-action/blob/main/docs/usage.md)
- [Claude Code CLI リファレンス](https://code.claude.com/docs/en/cli-reference)
- [FAQ](https://github.com/anthropics/claude-code-action/blob/main/docs/faq.md)
