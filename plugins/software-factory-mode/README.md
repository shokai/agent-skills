# software-factory-mode

Software Factory 2026秋の開発フローをsessionに適用するmode skillです。開発の開始時に手動で起動すると、着手前の質問、codexとのbug確認、draft PRの作成、対話コンテキストのexport、インラインレビューコメントの草稿、sanity-reviewという順序で作業を進めます。

repoのCLAUDE.mdを書き換えずに、自分のマシンにインストールするだけで開発フローを持ち込めます。

## 前提条件

- 主エージェントのmodel: Claude CodeのOpus以上のtier（Opus、Fable、Mythos等）で実行する。Sonnet・Haiku等の下位tierでは、modeを適用せずに停止する
- Codex CLIがある場合: modelはSol以上のflagship（Sol、Astra等）、reasoning effortはmedium以上に設定しておく。Luna・Terra等の軽量tierやSolより前の世代のmodelでは停止する
- Codex CLIが無い環境では、bug確認を `subagent-consultation` で行う。このmodeでのsanity-reviewにはCodex CLIが必須で、無ければ実行しない
- GitHub CLI（`gh`）がインストール済みで認証済みであること。PRの作成、対話コンテキストのPRコメントへの投稿、sanity-reviewに使用
- subagentを起動できる環境: Codex CLIが無い時のbug確認と、開発したsessionからのsanity-reviewは、subagentが行う

## 使い方

```bash
/software-factory-mode:software-factory-mode-2026aki
```

## インストール方法

マーケットプレイスを登録してから、pluginをインストールします。

```bash
/plugin marketplace add shokai/agent-skills
/plugin install software-factory-mode
```

依存する [codex-consultation](../codex-consultation/README.md)・[subagent-consultation](../subagent-consultation/README.md)・[conversation-context](../conversation-context/README.md)・[sanity-review](../sanity-review/README.md)・[kuden](../kuden/README.md) も一緒にインストールされます。

既にマーケットプレイスを登録済みの場合、新しいスキルをインストールするには [マーケットプレイスの更新](../../README.md#スキルをうまくインストールできない場合) が必要です。

他のpluginは [リポジトリルートのREADME](../../README.md) から探せます。
