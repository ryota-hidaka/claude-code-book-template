# claude-code-book-template

Claude Code を活用した GitHub リポジトリの自動化テンプレートです。Issue・PR への自動応答、コードレビュー、Issue トリアージなど、GitHub Actions による開発ワークフロー自動化をすぐに使い始めるための構成が含まれています。

## 概要

このテンプレートを使うことで、以下の自動化機能をリポジトリに導入できます。

| 機能 | 説明 |
|---|---|
| **@claude メンション応答** | Issue や PR コメントで `@claude` とメンションすると Claude が自動応答・作業を実施 |
| **自動コードレビュー** | PR が作成・更新されると Claude が自動でコードレビューを実施 |
| **Issue 自動トリアージ** | Issue が作成されると Claude が内容を分析してラベルを自動付与 |

## ディレクトリ構成

```
.
├── .claude/
│   └── commands/
│       └── label-issue.md        # Issue ラベル付けカスタムコマンド
├── .devcontainer/
│   ├── devcontainer.json         # Dev Container 設定
│   └── post_create.sh            # Dev Container 初期化スクリプト
├── .github/
│   └── workflows/
│       ├── claude.yml            # @claude メンション応答ワークフロー
│       ├── claude-code-review.yml # 自動コードレビューワークフロー
│       └── issue-triage.yml      # Issue 自動トリアージワークフロー
└── docs/
    └── github-actions.md         # GitHub Actions 設定ガイド
```

## セットアップ

### 1. リポジトリをテンプレートから作成

このリポジトリの **"Use this template"** ボタンから新しいリポジトリを作成します。

### 2. Claude Code OAuth トークンを取得

1. [Claude Code](https://claude.ai/code) にサインインします。
2. OAuth トークンを取得します。

### 3. シークレットを登録

リポジトリの **Settings → Secrets and variables → Actions** を開き、以下のシークレットを登録します。

| シークレット名 | 説明 |
|---|---|
| `CLAUDE_CODE_OAUTH_TOKEN` | Claude Code の認証トークン |

### 4. GitHub Actions を有効化

**Settings → Actions → General** で GitHub Actions が有効になっていることを確認します。

## 使い方

### @claude メンション

Issue や PR のコメントで `@claude` とメンションするだけで Claude が応答します。

```
@claude このバグを修正してください
@claude コードをレビューしてください
@claude テストを追加してください
```

### 自動コードレビュー

PR を作成するだけで自動的にコードレビューが実行されます。手動操作は不要です。

### Issue 自動トリアージ

Issue を作成すると、Claude が内容を分析して適切なラベルを自動的に付与します。

## ドキュメント

- [GitHub Actions ワークフロー設定ガイド](docs/github-actions.md)
- [claude-code-action ドキュメント](https://github.com/anthropics/claude-code-action/blob/main/docs/usage.md)
- [FAQ](https://github.com/anthropics/claude-code-action/blob/main/docs/faq.md)

## ライセンス

[LICENSE](LICENSE) を参照してください。
