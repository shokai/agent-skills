# software-factory-mode

Software Factory 2026秋の開発フローをsessionに適用するmode skillです。開発の開始時に手動で起動すると、着手前の質問、codexとのbug確認、draft PRの作成、対話コンテキストのexport、インラインレビューコメントの草稿、sanity-review、ready for review後のsquash and mergeのcommit message草稿の提示という順序で作業を進めます。

repoのCLAUDE.mdを書き換えずに、自分のマシンにインストールするだけで開発フローを持ち込めます。

## 前提条件

- 主エージェントのmodel: Claude CodeのOpus以上のtier（Opus、Fable、Mythos等）で実行する。Sonnet・Haiku等の下位tierでは、modeを適用せずに停止する
- Codex CLIがある場合: modelはSol以上のflagship（Sol、Astra等）、reasoning effortはmedium以上に設定しておく。Luna・Terra等の軽量tierやSolより前の世代のmodelでは停止する
- Codex CLIが無い環境では、bug確認を `subagent-consultation` で行う。このmodeでのsanity-reviewにはCodex CLIが必須で、無ければ実行しない
- GitHub CLI（`gh`）がインストール済みで認証済みであること。PRの作成、対話コンテキストのPRコメントへの投稿、sanity-reviewに使用
- subagentを起動できる環境: Codex CLIが無い時のbug確認と、開発したsessionからのsanity-reviewは、subagentが行う

## 使い方

2つのmodeがあります。開発の開始時にどちらか1つを起動します。

### software-factory-mode-2026aki

基本の開発フローです。インラインレビューコメントは草稿を提示して投稿方法を確認し、sanity-reviewはユーザーの指示を待ちます。

```bash
/software-factory-mode:software-factory-mode-2026aki
```

### software-factory-mode-2026hashi

2026akiのブレーキをかなり外したmodeです。インラインレビューコメントは草稿を提示せずに投稿し、sanity-review・指摘対応のpush・ready for reviewへの切り替えを指示を待たずに続けます。これらの上書きは、組織が配布する物も含め、CLAUDE.md・AGENTS.md等に書いた指示にも優先します。AIの作業の品質に信頼を得た後に使います。

```bash
/software-factory-mode:software-factory-mode-2026hashi
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
