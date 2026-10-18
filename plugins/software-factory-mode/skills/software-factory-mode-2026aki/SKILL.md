---
name: software-factory-mode-2026aki
description: >-
  Software Factory 2026秋の開発フローをこのsessionに適用する。
  着手前に不明点をユーザーに質問、変更が一段落したらcodexでbug確認、draft PR作成、対話コンテキストexport、インラインレビューコメントの草稿、ユーザーの指示でsanity-review、ready for review後のsquash and mergeのcommit message草稿の提示、という進め方を定める。
  開発の開始時にユーザーが手動で起動する。
disable-model-invocation: true
---

# Software Factory 2026秋 作業モード

このsessionでは以下の開発フローに従う。formatter・linter・testの実行方法や依存関係の準備といったrepo固有の手順は、repoのCLAUDE.mdやREADMEに従う。

## 実行環境の確認

modeを適用する前に実行環境を確認する。いずれかを満たさなければ、modeを適用せず、満たさない条件をユーザーに報告して停止する:

1. 自分のmodelがOpus以上のtierである（Opus、Fable、Mythos等）。Sonnet・Haiku等の下位tierは、新しい世代でも満たさない
2. Codex CLIがある場合、そのmodelがSol以上のflagship（Sol、Astra等）で、reasoning effortがmedium以上である。Bashツールで `command -v codex` を実行して有無を確認し、あれば短いプロンプトを作業ツリー外のファイルに書き、`codex exec --ephemeral` にstdinで渡して1回実行し、出力のヘッダから読む。Luna・Terra等の軽量tierと、Solより前の世代のmodelは相談相手として足りない。modelがどのtierかを判断できない時は、ユーザーに訊く前に、Codex CLIのmodelカタログでそのmodelの説明を確認する

## 基本フロー

1. 着手前に不明点・懸念事項をユーザーに質問し、解消してからplan modeに入る
2. 変更が一段落したらbug確認
3. formatterとlinterを実行
4. 必要に応じてcommit。default branchには直接commitしない
5. pushはユーザーの明示的な指示を待つ
6. PR作成を指示されたらdraftで作成し、対話コンテキストをexportし、インラインレビューコメントの草稿を書く
7. 草稿を提示したら投稿方法をユーザーに確認する。AIが投稿するか、ユーザー自身が投稿するか
8. sanity-reviewはユーザーの指示を待ち、指摘対応をpushし、対応内容を簡単にコメント投稿してからready for review
9. PRがready for reviewに切り替わったら、そのPRがsquash and mergeでcommit messageを指定できる場合は、1行目の既定値と本文の草稿を提示し、「指示されたらこの本文でsquash and mergeする」と告げる
10. mergeを指示されたらmergeする。ラベルでPRの状態を表しているrepoでは、レビュー依頼のラベルを外し、merge承認のラベルに付け替える
11. mergeされたら開発環境を後始末する

## 各手順の詳細

### bug確認

変更が一段落したら、codex-consultation skillでよく相談し、実装内容を確認する。testをbackgroundで実行している間は相談せず、testの完了を確かめてから相談する。Codexは実行不要と伝えても自分でtestを実行する事があるので、プロンプトにはtest・lint・build・コンテナ操作を実行しない事を書く。

Codex CLIが無い環境ではsubagent-consultation skillにフォールバックし、報告にその旨を明記する。subagentはOpus以上のtierのmodelを指定して起動する。Codexが利用制限や通信障害等で使えなくなった時はフォールバックせず、ユーザーに報告して判断を仰ぐ。

指摘された単純なbugは修正する。解決方法が複数ある場合はユーザーに質問する。ユーザーへの報告には、相談の深さと、相談の後に加えた変更の有無を書く。pushの前に、最後の相談より後に加えた変更が残っていないかを確かめ、残っていれば相談してからpushする。

### Gitの使い方

- commit前に現在のbranchを確認する。default branchにいる場合はcommitせず停止し、変更内容に基づいたbranch名を提案してユーザーに確認する
- branch名は、入力補完や一覧から探しやすくなるよう、最重要トピックを先頭に置いて `対象-性質` の順にする。各セグメントはcamelCaseで書き、セグメント間をハイフンでつなぐ。例: `reviewSkills-requireOpusModel`
  - `fix/` や `worktree-` 等の接頭辞を付けない
- `git commit --amend` やreflogを使った巻き戻しなど、commit済みの履歴を書き換える操作は勝手に行わない。必要だと判断した場合は、実行前にユーザーに提案して指示を仰ぐ
- 作業中に見つけた1行程度の付随的な修正は、別のbranchを切らず、今のbranchで独立したcommitにする。レビューの論点が増える修正は、別branchを提案する
- 作成済みのPRがあるbranchにpushしたら、対話コンテキストをexportする
- git worktreeは、main worktreeの `.claude/worktrees/<branch名>` に作る。作ったら、repoの手順に従って依存関係を用意する。main worktreeからコピーするかinstallする。開発環境の使い分けは「worktreeの開発環境」に従う

### pull requestの作成

- PRはdraftで作成する。draftは実装者が仕上げている途中の状態で、AIによるレビューはこの間に済ませる。ready for reviewは人間にレビューを依頼できる状態を表し、AIレビュー待ちの意味では使わない
- 概要欄はrepoのPRテンプレートに従う
- 子PRを作ったら、親子PRを相互にリンクする
- PRを作成したら、また作成後にpushしたら、conversation-context-export skillを実行する。worktreeで作業している場合は、出力先をmain worktreeの`.dev/contexts/`にする。報告では、ローカルのファイルのパスとPRコメントのURLを伝え、commitの可否に言及しない
- `.dev/contexts/`がrepoでgit管理もignoreもされていない場合、exportしたファイルはcommitに含めず、gitignoreに加える事をユーザーに提案する
- PRを作成したら、ユーザーがレビュアーに実装を説明するためのインラインレビューコメントの草稿を書き、ユーザーに提示する。重要な変更に絞り、ファイル名と行番号を付ける

### レビュー

- sanity-review skillの実行者はレビュアーではなく実装者。概要欄を書き終えてから、レビュアーに依頼する前に実行する
- 変更の規模を理由にsanity-reviewを省略しない。1行の修正でも複数の問題が指摘される事がよくある
- インラインレビューコメントを投稿してからsanity-reviewを実行するのが望ましい。コメントでの実装者の説明と実装の整合性を確認する材料になる
- 開発したsessionでsanity-reviewを実行する時は、開発sessionの会話履歴を引き継がない、Opus以上のtierのsubagentを起動する。subagentにはPRのURL、実行条件、報告書の出力先だけを渡す。出力先はチャットとpull requestの両方に固定する。実行条件には次を含める
  - 外部Agent相談のフォールバック手順を使わない
  - 一時ファイルはscratchpad内のPRごとのディレクトリに置く
  - コンテナ操作とtest・lint・buildを実行せず、相談するCodexにも実行させない
  - 外部ツールに渡すプロンプトのファイルは、本文がシェルに展開されない方法で書く。Bashのheredocを使うならクォートする
  - Bashの呼び出しごとに作業ディレクトリを明示する
- subagentがcodex-consultation skillを呼んだ事は、報告の自己申告ではなく、subagentの記録にあるSkillツールの呼び出しを数えて確かめる。skillを呼ばずにCodexを直接実行して作った報告書は信用しない
- sanity-reviewの前提: 対話コンテキストがPRコメントに投稿されている事。同じマシンにCodex CLIがあり、modelとreasoning effortが「実行環境の確認」の条件を満たす事。Codex CLIが無ければ実行せず、Codexとの相談に失敗したら中止する。Opus以上のtierを使う。Sonnetではレビューできない
- sanity-reviewが済むまでPRはdraftのままにする。指摘への対応をpushし終えてから、実装者がdraftからready for reviewに切り替える。切り替えと同時に、そのrepoがレビュー依頼に使うラベルやreviewerを付ける

### worktreeの開発環境

- worktreeの作業内容のtest・lint・build・検証scriptの実行は、worktree専用のコンテナ環境で行う。コンテナのnetwork・volume・imageをworktree毎に分ける。並行して動いている別のworktreeの環境と同じnetworkに乗ると、同じ名前のDBやキャッシュへの名前解決がどちらに向くか定まらなくなる。起動は成功するのでportの衝突と違って気付きにくく、testが実行ごとに違う箇所で落ちる形で現れる
- host側のportを公開するのは、hostから接続して確かめる環境だけにする。testはコンテナ間のnetworkで繋がる
- ローカルマシンに溜まっているデータを使って動作確認やUI確認をする時だけ、main worktreeの環境で動いているDBに接続する。接続先のDBは動いている物をそのまま使い、作り直さない
- 環境を起動する操作をした時は、自分が新しく起動した物と、既に動いていた物に繋いだだけの物を区別して把握する
- コンテナ環境を操作するコマンドは、呼び出しごとに作業ディレクトリを明示する。作業ディレクトリはmain worktreeに戻っている事があり、そのまま実行するとworktreeのつもりでmain worktreeをbuildする

### 開発環境の後始末

- 起動した開発サーバーやコンテナは、目視確認・test・lint・Codexとの相談といった、それを使う作業が終わったら止める。再び要る時に起動し直す
- 他のworktreeやsessionが起動した物は、ユーザーの指示があるまで止めない。止めてよいのは自分が新しく起動した物だけである。止める前に、そのコンテナを別のworktreeやsessionのプロセスが使っていないかを確かめる
- mergeされたら、作業に使ったworktreeとbranchを消す。worktree専用に作られたコンテナのnetwork・volume・image等も一緒に消す。main worktreeの物と、他のworktreeやsessionと共有している物は残す
- mergeされたら、main worktreeのdefault branchをremoteのdefault branchに追従させる。次のどれかに当たる時は追従させず、理由を報告する
  - main worktreeがdefault branch以外にいる
  - main worktreeの追跡済みファイルに未commitの変更があるか、追従で書き換わる未追跡ファイルがある
  - main worktreeを起動中のコンテナや開発サーバー・testが使っている
  - 他のsessionがmain worktreeで作業を実行している。入力待ちで止まっているsessionは数えない

## 手順全体を通して守る事

### ユーザーの判断の得方

kuden:user-decision skillを読み込み、その基準に従う。

### ソースコード中のコメントの書き方

kuden:code-comment skillを読み込み、その基準に従う。

### Agent Skillの書き方

kuden:agent-skill skillを読み込み、その基準に従う。

### CLAUDE.md・AGENTS.md等のルール文書の書き方

kuden:persistent-instructions skillを読み込み、その基準に従う。

### pull requestとGitHubに載る文章の書き方

kuden:github skillを読み込み、その基準に従う。

### ソフトウェアテストの書き方

kuden:software-test skillを読み込み、その基準に従う。

### 指示元のドキュメントとの関連付け

タスクの指示元が作業ページ・issue・チケット等のドキュメントにある時に行う。記法や状態の表し方はプロジェクトの慣習に従い、既存の記述に合わせる。

- 作業を開始したら、指示元に自分が着手した事を書く。着手表明は、担当者であるユーザーの名義で書く。AIの発言への署名はプロジェクトの慣習に従う
- PRの概要欄に指示元のURLを書き、指示元にはPRへのリンクを足す
- draft PR作成・ready for review・mergeの各時点で、指示元と、それを載せている親のタスクまとめページの状態を同期する。ready for reviewのPRは、approveされていてもmergeされるまでレビュー待ちの状態にする。mergeは実際にmergeされた事を確かめてから反映する

### 共有テキストの編集

PR概要欄や共有ドキュメントを編集する時は、直前に最新版を取得し、自分が元にした版から変わっていない事を確かめてから書く。変わっていれば最新版の上に自分の変更を当て直す。
