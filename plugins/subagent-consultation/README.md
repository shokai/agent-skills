# subagent-consultation

Agentツール（subagent）にセカンドオピニオンを求めるAgent Skillです。

[codex-consultation](../codex-consultation/README.md) と同じ批判的思考の連鎖プロトコルを、Codex CLIの代わりにAgentツール（subagent）で実行します。Codex CLIがインストールされていない環境でも利用できます。

## 機能

- 会話コンテキストに基づいてsubagentへの相談プロンプトを自動設計
- subagentの回答を要約・整理し、自身の見解と照らし合わせて報告
- subagentの実行失敗を検知した場合、相談元Agentが補正

## 使い方

```bash
/subagent-consultation
```

作業中に「subagentと相談して」「subagentに聞いて」「subagentにレビューしてもらって」と伝えると発動します。

## インストール方法

マーケットプレイスを登録してから、pluginをインストールします。

```bash
/plugin marketplace add shokai/agent-skills
/plugin install subagent-consultation
```

既にマーケットプレイスを登録済みの場合、新しいスキルをインストールするには [マーケットプレイスの更新](../../README.md#スキルをうまくインストールできない場合) が必要です。

他のpluginは [リポジトリルートのREADME](../../README.md) から探せます。
