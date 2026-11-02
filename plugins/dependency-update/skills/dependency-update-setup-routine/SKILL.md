---
name: dependency-update-setup-routine
description: >-
  dependency-update skillの起動プロンプトを、実行に必要な値を訊いて作る。
  Claude Code on the webのroutineを作成・更新する手段があれば、routineの登録・更新まで行う。
  ユーザーが手動で起動する。
argument-hint: "[起動プロンプトだけ]"
disable-model-invocation: true
---

# 定期処理の起動プロンプトの作成と、routineへの登録

dependency-update skillを定期的に実行する起動プロンプトを作り、Claude Code on the webのroutineに登録する。routineを扱う手段が無い環境と、「起動プロンプトだけ」と指示された時は、起動プロンプトと、routineの設定で決める事を出力し、登録は人間に任せる。

## 起動プロンプトを作る

実行に必要な値の定義は、[dependency-update skill](../dependency-update/SKILL.md) の「起動プロンプトから受け取る値」にある。それを読み、値をユーザーに訊いて埋める。初めて対象にするリポジトリの自動化段階は「レビューまで」を勧める。レビューの出来を人間が見ながら、徐々に自動化段階を上げる。

起動プロンプトには、dependency-update skillを実行する事と、値だけを書く。手順はskillにあるので、書き写さない。待ち時間のような例外をユーザーが求めた時は、skillに優先する指示として起動プロンプトに書く。同じ起動プロンプトを、手元の手動実行にも使える。

```
dependency-update skillを実行する。

対象リポジトリと自動化段階:
- {owner/repo}: mergeまで
- {owner/repo}: レビューまで

ラベル: レビュー依頼は `{名前}`、merge承認は `{名前}`、セキュリティ更新は `{名前}`
failしていてもmergeするCI: {owner/repo} の `{checkの名前}`
通知先: {通知先}
```

ラベルを使わないリポジトリだけなら、ラベルの行を省く。failしていてもmergeするCIは、mergeを止めなくてよいとユーザーが決めたcheckがある時だけ書き、無ければ行を省く。checkの名前は、pull requestのcheckの一覧に出る名前をそのまま書く。何も書かなければ、全てのCIのcheckが失敗せずに終わっている事がmergeの条件になる。

## routineに登録する

このsessionに、Claude Code on the webのroutineを一覧・作成・更新する手段があれば、それを使う。使い方は、手段自身の説明に従う。手段が無ければ、「人間に登録を任せる」に進む。

既存のroutineを一覧し、dependency-update skillを起動する物か、依存関係の更新pull requestのレビューやmergeを行う物があれば、新しく作らず、そのroutineのプロンプトを起動プロンプトに置き換える更新を勧める。

routineの設定で決める事と、その決め方:

- 名前
- 実行の時刻と周期。ユーザーの現地時刻で訊き、routineの時刻の表現に直して、ユーザーに確かめる
- 実行環境
- model。dependency-update skillの「実行環境の確認」の条件を満たす物
- routineがcheckoutするリポジトリ。対象リポジトリの一覧と同じにする。対象リポジトリのコードを、指揮役が起動するsubagentが読める環境にする
- 通知先に投稿するコネクタ。通知先がSlackのチャンネルなら、Slackのコネクタである
- plugin。routineの設定でpluginを有効にできるなら、dependency-updateと `kuden` を有効にする。できなければ、実行環境にこの2つをinstallしておく事をユーザーに伝える

作成・更新の前に、設定の全体をユーザーに見せて確認する。作成・更新したら、routineの場所を伝え、1回手動で実行して様子を見る事を勧める。

## 人間に登録を任せる

起動プロンプトをコードブロックで出力し、「routineの設定で決める事」を箇条書きで添え、routineへの登録は人間が行うと告げる。
