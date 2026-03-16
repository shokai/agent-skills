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
/plugin install conversation-context
/plugin install pr-review
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

### pr-review

feature/bugfix/refactoring PRのレビュー報告書を作成するスキルです。

PR概要欄の品質評価、説明と実装の整合性確認、バグ・脆弱性調査、対話コンテキストを用いた考慮漏れ確認を行い、レビュアーがGitHubに貼れるレビュー報告書を出力します。

前提: GitHub CLI（`gh`）がインストール済みで認証済みであること。codex-consultationスキルとconversation-contextスキルがインストールされていると、バグ調査や考慮漏れ確認の精度が向上します（未インストールの場合はAgent単独でレビューを続行します）。

#### 機能

- PR概要欄の品質評価（変更前後の説明、実装者の理解度など4項目）
- 実装者の説明と実際のコード差分の整合性確認
- Codexと連携したバグ・脆弱性の調査
- 対話コンテキストを用いた設計判断・考慮漏れの検証
- レビュー報告書の生成

#### 使い方

```bash
/pr-review [PR-URL-or-number]
```

PRブランチで実行するか、PR URLまたは番号を指定すると、レビュー報告書を作成します。
