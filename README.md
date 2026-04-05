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
/plugin install subagent-consultation
/plugin install conversation-context
/plugin install sanity-review
/plugin install prose-proofreading
```

### スキルをうまくインストールできない場合

既にマーケットプレイスを登録済みの場合、新しいスキルをインストールするにはマーケットプレイスの更新が必要です。

1. `/plugin` コマンドを実行
2. 「Update marketplace」を選択
3. 「Browse plugins」から新しいスキルをインストール

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

- 会話コンテキストに基づいてCodexへの相談プロンプトを自動設計
- Codexの回答をClaudeが要約・整理し、自身の見解と照らし合わせて報告
- Codexのコマンド失敗を検知した場合、Claudeが補正

#### 使い方

```bash
/codex-consultation
```

作業中に「codexと相談して」「codexに聞いて」「codexにレビューしてもらって」と伝えると発動します。

### subagent-consultation

Agentツール（subagent）にセカンドオピニオンを求めるスキルです。

codex-consultationと同じ批判的思考の連鎖プロトコルを、Codex CLIの代わりにAgentツール（subagent）で実行します。Codex CLIがインストールされていない環境でも利用できます。

#### 機能

- 会話コンテキストに基づいてsubagentへの相談プロンプトを自動設計
- subagentの回答を要約・整理し、自身の見解と照らし合わせて報告
- subagentの実行失敗を検知した場合、相談元Agentが補正

#### 使い方

```bash
/subagent-consultation
```

作業中に「subagentと相談して」「subagentに聞いて」「subagentにレビューしてもらって」と伝えると発動します。

### conversation-context

対話コンテキストのexport/importスキルです。2つのスキルがセットでインストールされます。

会話で共有された目的・意図・設計判断・制約条件を `.dev/contexts/` ディレクトリに書き出し、別セッションやレビューで読み込むことができます。1対多のコンテキスト共有により、複数のsub pull requestをまとめたfeature pull requestのレビューやコードの自動改善に便利です。

#### 機能

- **conversation-context-export**: 現在の会話コンテキスト（目的、設計判断、制約条件など）を `.dev/contexts/` に書き出す
- **conversation-context-import**: `.dev/contexts/` に保存されたコンテキストを読み込み、現在のセッションに反映する

#### 使い方

```bash
/conversation-context-export
/conversation-context-import
```

### sanity-review

PRのレビュー報告書を作成するスキルです。bugや脆弱性の調査だけでなく、exportされた対話コンテキスト・PR概要欄・実装されたコードの整合性を確認し、実装者の正気を疑います。

詳細は [plugins/sanity-review/README.md](plugins/sanity-review/README.md) を参照してください。

```bash
/sanity-review [PR-URL-or-number]
```

### prose-proofreading

Markdownドキュメントの文章校正スキルです。ガイドラインに基づいて文体の問題を検出し、修正案を提示します。

```bash
/prose-proofreading [file-path or branch-diff]
```
