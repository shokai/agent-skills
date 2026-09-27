---
name: software-factory-mode-2026aki
description: >-
  Software Factory 2026秋の開発フローをこのsessionに適用する。
  着手前に不明点をユーザーに質問、変更が一段落したらcodexでbug確認、draft PR作成、対話コンテキストexport、インラインレビューコメントの草稿、ユーザーの指示でsanity-review、という進め方を定める。
  開発の開始時にユーザーが手動で起動する。
disable-model-invocation: true
---

# Software Factory 2026秋 作業モード

このsessionでは以下の開発フローに従う。formatter・linter・testの実行方法や依存関係の準備といったrepo固有の手順は、repoのCLAUDE.mdやREADMEに従う。

## 実行環境の確認

modeを適用する前に実行環境を確認する。いずれかを満たさなければ、modeを適用せず、満たさない条件をユーザーに報告して停止する:

1. 自分のmodelがOpus以上のtierである（Opus、Fable、Mythos等）。Sonnet・Haiku等の下位tierは、新しい世代でも満たさない
2. Codex CLIがある場合、そのmodelがSol以上のflagshipで、reasoning effortがmedium以上である。Bashツールで `command -v codex` を実行して有無を確認し、あれば短いプロンプトを作業ツリー外のファイルに書き、`codex exec --ephemeral` にstdinで渡して1回実行し、出力のヘッダから読む。Luna・Terra等の軽量tierと、Solより前の世代のmodelは相談相手として足りない

## 基本フロー

1. 着手前に不明点・懸念事項をユーザーに質問し、解消してからplan modeに入る
2. 変更が一段落したらbug確認
3. formatterとlinterを実行
4. 必要に応じてcommit。default branchには直接commitしない
5. pushはユーザーの明示的な指示を待つ
6. PR作成を指示されたらdraftで作成し、対話コンテキストをexportし、インラインレビューコメントの草稿を書く
7. 草稿を提示したら投稿方法をユーザーに確認する。AIが投稿するか、ユーザー自身が投稿するか
8. sanity-reviewはユーザーの指示を待ち、指摘対応をpushし、対応内容を簡単にコメント投稿してからready for review
9. mergeを知らされたら開発環境を後始末する

## ソースコード中のコメントの書き方

コメントに書いてよいのは、コードを読んでも分からない非自明な制約・invariant・隠れ仕様・gotcha・特定bugへのworkaroundのみ。意図（なぜそうするか）は書いてよい。

- 特定のオプション値にまつわるgotchaは、関数の上にまとめず該当行の行末に置く
- 「なぜ」を書く前に、その動機がコードの式から読めないか確認する。読めるなら書かない。書くとしたらcommit messageかPR概要欄に置く

## bug確認

変更が一段落したら、codex-consultation skillでよく相談し、実装内容を確認する。単純なbugであれば修正する。解決方法が複数ある場合はユーザーに質問する。

Codex CLIが無い環境ではsubagent-consultation skillにフォールバックし、報告にその旨を明記する。subagentはOpus以上のtierのmodelを指定して起動する。Codexが利用制限や通信障害等で使えなくなった時はフォールバックせず、ユーザーに報告して判断を仰ぐ。

## Gitの使い方

- commit前に現在のbranchを確認する。default branchにいる場合はcommitせず停止し、変更内容に基づいたbranch名を提案してユーザーに確認する
- branch名は、入力補完や一覧から探しやすくなるよう、最重要トピックを先頭に置いて `対象-性質` の順にする。各セグメントはcamelCaseで書き、セグメント間をハイフンでつなぐ。例: `reviewSkills-requireOpusModel`
  - `fix/` や `worktree-` 等の接頭辞を付けない
- `git commit --amend` やreflogを使った巻き戻しなど、commit済みの履歴を書き換える操作は勝手に行わない。必要だと判断した場合は、実行前にユーザーに提案して指示を仰ぐ
- 作成済みのPRがあるbranchにpushしたら、対話コンテキストをexportする
- git worktreeを作ったら、repoの手順に従って依存関係を用意する。main worktreeからコピーするかinstallする

## pull requestの作成

- PRはdraftで作成する。draftは実装者が仕上げている途中の状態で、AIによるレビューはこの間に済ませる。ready for reviewは人間にレビューを依頼できる状態を表し、AIレビュー待ちの意味では使わない
- 概要欄はrepoのPRテンプレートに従う
- PRを作成したら、また作成後にpushしたら、conversation-context-export skillを実行する。worktreeで作業している場合は、出力先をmain worktreeの`.dev/contexts/`にする
- `.dev/contexts/`がrepoでgit管理もignoreもされていない場合、exportしたファイルはcommitに含めない
- PRを作成したら、ユーザーがレビュアーに実装を説明するためのインラインレビューコメントの草稿を書き、ユーザーに提示する。重要な変更に絞り、ファイル名と行番号を付ける

## 指示元のドキュメントとの関連付け

タスクの指示元が作業ページ・issue・チケット等のドキュメントにある時に行う。記法や状態の表し方はプロジェクトの慣習に従い、既存の記述に合わせる。

- 作業を開始したら、指示元に自分が着手した事を書く
- PRの概要欄に指示元のURLを書き、指示元にはPRへのリンクを足す。親PRに向けたsub PRを作った時も、親子の両方から相互にリンクする
- draft PR作成・ready for review・mergeの各時点で、指示元の作業ページと、それを載せている親のタスクまとめページの状態を同期する。mergeは`gh pr view`で確かめてから反映する
- PR概要欄や共有ドキュメントを編集する時は、直前に最新版を取得し、自分が元にした版から変わっていない事を確かめてから書く。変わっていれば最新版の上に自分の変更を当て直す

## 開発環境の後始末

- 起動した開発サーバーやコンテナは、目視確認・test・lint・Codexとの相談といった、それを使う作業が終わったら止める。再び要る時に起動し直す
- 他のworktreeやsessionが起動した物は、ユーザーの指示があるまで止めない
- mergeされたら、作業に使ったworktreeとbranchを消す。branchはremoteのbranchと一致する事を確かめてから消す。worktree専用に作られたdocker volumeやimage等も一緒に消す。main worktreeの物は残す

## レビュー

- sanity-review skillの実行者はレビュアーではなく実装者。概要欄を書き終えてから、レビュアーに依頼する前に実行する
- 変更の規模を理由にsanity-reviewを省略しない。1行の修正でも複数の問題が指摘される事がよくある
- インラインレビューコメントを投稿してからsanity-reviewを実行するのが望ましい。コメントでの実装者の説明と実装の整合性を確認する材料になる
- 開発したsessionでsanity-reviewを依頼された時は、開発sessionの会話履歴を引き継がない、Opus以上のtierのsubagentを起動する。subagentにはPRのURL、実行条件、報告書の出力先だけを渡す。出力先はチャットとpull requestの両方に固定する。skillのフォールバック手順を使わない事も指示に含める
- sanity-reviewの前提: 対話コンテキストがPRコメントに投稿されている事。同じマシンにCodex CLIがあり、modelとreasoning effortが「実行環境の確認」の条件を満たす事。Codex CLIが無ければ実行せず、Codexとの相談に失敗したら中止する。Opus以上のtierを使う。Sonnetではレビューできない
- sanity-reviewが済むまでPRはdraftのままにする。指摘への対応をpushし終えてから、実装者がdraftからready for reviewに切り替える
