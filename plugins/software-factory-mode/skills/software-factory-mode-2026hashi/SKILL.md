---
name: software-factory-mode-2026hashi
description: >-
  software-factory-mode-2026akiの開発フローに、作者が各repoで上書きしてきた動作を重ねる。
  ファイル変更はgit worktreeで行い、bug確認は変更のたびによく相談し、インラインレビューコメントは草稿を提示せずに投稿し、sanity-review・指摘対応のpush・ready for reviewへの切り替えを指示を待たずに続ける。
  開発の開始時にユーザーが手動で起動する。
---

# Software Factory 2026hashi作業モード

このsessionでは、software-factory-mode-2026akiの開発フローに、以下の上書きを重ねて作業する。最初にsoftware-factory-mode-2026aki skillを読み込み、実行環境の確認を含めてその手順に従う。以下に書いた事は、2026akiの対応する記述に優先する。

## 実行環境の確認

- Codexのmodelがどのtierかを判断できない時は、ユーザーに訊く前に、Codex CLIのmodelカタログでそのmodelの説明を確認する

## 基本フローの上書き

- ファイル変更を伴う作業は、default branchのcheckoutを直接編集せず、作業branchのgit worktreeを作ってその中で行う。計画の最初の手順にworktreeの作成を入れる。置き場所はmain worktreeの `.claude/worktrees/<branch名>` にし、兄弟ディレクトリに作らない。現在の作業ツリーが既にdefault branch以外の作業branchにいる時は、worktreeを作らずそのまま作業してよい
- bug確認は、変更のたびにcodex-consultation skillの「よく相談する」深さで行う。PR作成後の追加の変更も、Codexやsanity-reviewが示した修正案をそのまま実装した変更も同じにする
- pushの前に、最後の相談より後に加えた変更が残っていないかを確かめる。残っていれば相談してからpushする
- sanity-reviewの指摘対応のpushは、ユーザーの指示を待たずに行う。それ以外のpushは指示を待つ
- インラインレビューコメントの草稿は、ユーザーに提示して投稿方法を確認せず、そのまま自分で投稿する。投稿後に誤りが見つかったら、コメントを編集して直す
- インラインレビューコメントを投稿したら、ユーザーの指示を待たずにsanity-reviewを実行する
- sanity-reviewの指摘への対応を全て済ませ、pushし、対応内容をコメント投稿したら、ユーザーの指示を待たずにdraftからready for reviewに切り替える。切り替えと同時に、そのrepoがレビュー依頼に使うラベルやreviewerを付ける。ユーザーの判断が要る指摘が残っている時は、訊いて対応を済ませてから切り替える
- ラベルでPRの状態を表しているrepoでは、mergeの時にレビュー依頼のラベルを外し、merge承認のラベルに付け替える

## 各手順の詳細の上書き

### bug確認

- testをbackgroundで実行している間は、Codexに相談しない。Codexは実行不要と伝えても自分でtestを実行する事がある。testの完了を確かめてから相談し、プロンプトにはtest・lint・build・コンテナ操作を実行しない事を書く
- ユーザーへの報告には、相談の深さと、相談の後に加えた変更の有無を書く

### Gitの使い方

- 作業中に見つけた1行程度の付随的な修正は、別のbranchを切らず、今のbranchで独立したcommitにする。レビューの論点が増える修正は、別branchを提案する

### pull requestの作成

- 対話コンテキストのexportは、PR作成時、PR作成後のpush時、ユーザーが明示的に指示した時だけ行う。実装が変わるたびに自発的に更新しない。完了報告ではローカルのファイルのパスとPRコメントのURLだけを伝え、exportしたファイルのcommitの可否に言及しない
- repoがpublicの時は、PR概要欄、コメント、インラインレビューコメント、対話コンテキスト、commit message、sanity-reviewの報告書に、社内のリポジトリ名、社内のPR番号、デプロイ手段、社内作業の実施時期が分かる日付を書かない。判断に要る性質だけを「あるリポジトリ」のように伏せて書く。subagentに書かせる物も対象なので、subagentへの指示に含める。名前を出したい時はユーザーに確認する

### レビュー

- sanity-reviewのsubagentへの指示に、次を含める
  - 一時ファイルはscratchpad内のPRごとのディレクトリに置く
  - コンテナ操作とtest・lint・buildを実行せず、相談するCodexにも実行させない。外部ツールに渡すプロンプトのファイルは、本文がシェルに展開されない方法で書く。Bashのheredocを使うならクォートする。Bashの呼び出しごとに作業ディレクトリを明示する
- subagentがcodex-consultation skillを呼んだ事は、報告の自己申告ではなく、subagentの記録にあるSkillツールの呼び出しを数えて確かめる。skillを呼ばずにCodexを直接実行して作った報告書は信用しない

## 手順全体を通して守る事の上書き

### 指示元のドキュメントとの関連付け

- 着手表明は、担当者であるユーザーの名義で書く。AIの発言への署名はプロジェクトの慣習に従う
- ready for reviewのPRは、approveされていてもmergeされるまでレビュー待ちの状態にする
