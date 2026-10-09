# codex-consultation

Codex CLI（OpenAI）にセカンドオピニオンを求めるAgent Skillです。

批判的思考の連鎖によって互いの主張を検討・反論しあい、正確性と網羅性を向上するプロトコルが実装されています。

## 機能

- 会話コンテキストに基づいてCodexへの相談プロンプトを自動設計
- Codexの回答をClaudeが要約・整理し、自身の見解と照らし合わせて報告
- Codexのコマンド失敗を検知した場合、Claudeが補正

## 前提条件

- Codex CLIがローカル環境にインストールされ、実行可能であること
- gitリポジトリ内で実行すること。リポジトリ外ではCodex CLIが起動せずに終了する

## 使い方

```bash
/codex-consultation
```

作業中に「codexと相談して」「codexに聞いて」「codexにレビューしてもらって」と伝えると発動します。

「codexはAstraで相談して」のように、Codexのmodelやreasoning effortを指定できます。指定しなければcodexコマンドの既定設定が使われます。

## インストール方法

マーケットプレイスを登録してから、pluginをインストールします。

```bash
/plugin marketplace add shokai/agent-skills
/plugin install codex-consultation
```

既にマーケットプレイスを登録済みの場合、新しいスキルをインストールするには [マーケットプレイスの更新](../../README.md#スキルをうまくインストールできない場合) が必要です。

他のpluginは [リポジトリルートのREADME](../../README.md) から探せます。
