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

## 基本フロー

1. 着手前に不明点・懸念事項をユーザーに質問し、解消してからplan modeに入る
2. 変更が一段落したらbug確認
3. formatterとlinterを実行
4. 必要に応じてcommit。default branchには直接commitしない
5. pushはユーザーの明示的な指示を待つ
6. PR作成を指示されたらdraftで作成し、対話コンテキストをexportし、インラインレビューコメントの草稿を書く
7. 草稿を提示したら投稿方法をユーザーに確認する。AIが投稿するか、ユーザー自身が投稿するか
8. sanity-reviewはユーザーの指示を待ち、指摘対応をpushし、対応内容を簡単にコメント投稿してからready for review

## bug確認

変更が一段落したら、codex-consultation skillでよく相談し、実装内容を確認する。単純なbugであれば修正する。解決方法が複数ある場合はユーザーに質問する。

Codex CLIが無い環境ではsubagent-consultation skillにフォールバックし、報告にその旨を明記する。Codexが利用制限や通信障害等で使えなくなった時はフォールバックせず、ユーザーに報告して判断を仰ぐ。

## Gitの使い方

- commit前に現在のbranchを確認する。default branchにいる場合はcommitせず停止し、変更内容に基づいたbranch名を提案してユーザーに確認する
- 作成済みのPRがあるbranchにpushしたら、対話コンテキストをexportする
- git worktreeを作ったら、repoの手順に従って依存関係を用意する。main worktreeからコピーするかinstallする

## pull requestの作成

- PRはdraftで作成する。draftは実装者が仕上げている途中の状態で、AIによるレビューはこの間に済ませる。ready for reviewは人間にレビューを依頼できる状態を表し、AIレビュー待ちの意味では使わない
- 概要欄はrepoのPRテンプレートに従う
- PRを作成したら、また作成後にpushしたら、conversation-context-export skillを実行する。worktreeで作業している場合は、出力先をmain worktreeの`.dev/contexts/`にする
- `.dev/contexts/`がrepoでgit管理もignoreもされていない場合、exportしたファイルはcommitに含めない
- PRを作成したら、ユーザーがレビュアーに実装を説明するためのインラインレビューコメントの草稿を書き、ユーザーに提示する。重要な変更に絞り、ファイル名と行番号を付ける

## レビュー

- sanity-review skillの実行者はレビュアーではなく実装者。概要欄を書き終えてから、レビュアーに依頼する前に実行する
- 変更の規模を理由にsanity-reviewを省略しない。1行の修正でも複数の問題が指摘される事がよくある
- インラインレビューコメントを投稿してからsanity-reviewを実行するのが望ましい。コメントでの実装者の説明と実装の整合性を確認する材料になる
- 開発したsessionでsanity-reviewを依頼された時は、開発sessionの会話履歴を引き継がないOpusのsubagentを起動する。subagentにはPRのURL、実行条件、報告書の出力先だけを渡す。出力先はチャットとpull requestの両方に固定する。skillのフォールバック手順を使わない事も指示に含める
- sanity-reviewの前提: 対話コンテキストがPRコメントに投稿されている事。同じマシンにCodex CLIがある事。Codex CLIが無ければ実行せず、Codexとの相談に失敗したら中止する。Opus以上を使う。Sonnetではレビューできない
- sanity-reviewが済むまでPRはdraftのままにする。指摘への対応をpushし終えてから、実装者がdraftからready for reviewに切り替える
