# kuden

作者がAIとの様々な作業の中で重ねてきた失敗と成功のmemoryから抽出した心得を集めたガイドライン群です。AIが必要だと判断した時に読み込みます。手動でも起動できます。

## 含まれるスキル

- **agent-skill**: Agent Skillを書く時に、何を書き、何を書かないかの判断基準と、レビュー指摘の採否の基準
- **code-comment**: ソースコード中のコメントに、何を書き、何を書かないかの判断基準と、置く位置、日本語の文体
- **github**: pull requestのタイトルと概要欄の書き方、リンクが壊れない書き方、親子PRの組み方
- **software-test**: 何をtestし、何をtestしないかの判断基準と、アクセス権限のtestの書き方、落ちたtestの直し方、fixtureの仮名
- **orchestrator**: subagentに作業を任せる指揮役の心得。報告の確かめ方、進み具合の持ち方、止まったsubagentの扱い、利用上限との付き合い方

## 使い方

```bash
/kuden:agent-skill
/kuden:code-comment
/kuden:github
/kuden:software-test
/kuden:orchestrator
```

## インストール方法

マーケットプレイスを登録してから、pluginをインストールします。

```bash
/plugin marketplace add shokai/agent-skills
/plugin install kuden
```

既にマーケットプレイスを登録済みの場合、新しいスキルをインストールするには [マーケットプレイスの更新](../../README.md#スキルをうまくインストールできない場合) が必要です。

他のpluginは [リポジトリルートのREADME](../../README.md) から探せます。
