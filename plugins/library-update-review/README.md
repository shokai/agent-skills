# library-update-review

ライブラリ更新pull requestのレビューを支援するAgent Skillです。

## 機能

- dependabotやrenovatebotが作成したPRの分析
- release noteやchangelogの詳細調査
- ライブラリの依存関係の確認
- コードの使用箇所の特定
- 必要に応じたコード更新の提案
- 過去の失敗事例の調査

## 前提条件

- 実行するmodelが次のいずれかであること。当てはまらない場合は、レポートを作成せずに停止する
  - ClaudeのOpus以上のtier（Opus、Fable、Mythos等）。Sonnet・Haiku等の下位tierは、新しい世代であっても当てはまらない
  - CodexのSol以上のflagship（Sol、Astra等）で、reasoning effortがmedium以上。Luna・Terra等の軽量tierと、Solより前の世代のmodelは当てはまらない

## 使い方

```bash
/library-update-review [PR-URL-or-number]
```

dependabotやrenovatebotのPRブランチで実行すると、包括的なレビューレポートを作成します。

## インストール方法

マーケットプレイスを登録してから、pluginをインストールします。

```bash
/plugin marketplace add shokai/agent-skills
/plugin install library-update-review
```

既にマーケットプレイスを登録済みの場合、新しいスキルをインストールするには [マーケットプレイスの更新](../../README.md#スキルをうまくインストールできない場合) が必要です。

他のpluginは [リポジトリルートのREADME](../../README.md) から探せます。
