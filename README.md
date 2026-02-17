# Claude Code Agent Skills by shokai

- https://github.com/shokai/agent-skills

## インストール方法

### 1. マーケットプレイスを登録

```bash
/plugin marketplace add shokai/agent-skills
```

### 2. スキルをインストール

```bash
/plugin install library-update-review
/plugin install codex-consultation
```

## 含まれるスキル

### library-update-review

ライブラリ更新pull requestのレビューを支援するスキルです。

#### 機能

- dependabotやrenovatebotが作成したPRの分析
- release noteやchangelogの詳細調査
- ライブラリの依存関係の確認
- コードの使用箇所の特定
- 必要に応じたコード更新の提案
- 過去の失敗事例の調査

#### 使い方

```bash
/library-update-review [PR-URL-or-number]
```

dependabotやrenovatebotのPRブランチで実行すると、包括的なレビューレポートを作成します。

### codex-consultation

Codex CLI（OpenAI）にセカンドオピニオンを求めるスキルです。

前提: Codex CLIがローカル環境にインストールされ、実行可能であること。

#### 機能

- 会話コンテキストに基づいてCodexにプロンプトを自動設計
- Codexの回答をClaudeが要約・整理し、自身の見解と照らし合わせて報告
- Codexのコマンド失敗を検知した場合、Claudeが補正

#### 使い方

```bash
/codex-consultation
```

作業中に「codexと相談して」「codexに聞いて」「codexにレビューしてもらって」と伝えると発動します。
