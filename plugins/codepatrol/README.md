# codepatrol

リポジトリを領域ごとに巡回してセキュリティ調査するAgent Skillです。起動したsessionが指揮役になり、領域ごとに起動したsubagentが、調査対象リストと観点チェックリストに基づいて調査し、Codexによる批判的レビューを経てレポートを出力します。レポートの問題をトリアージし、subagentに自動修正させる事もできます。

## 背景

セキュリティ調査は一度で終わりません。コードは変わり続け、観点は増え続け、1回のsessionで見切れる範囲には限りがあります。機械的なスキャナは既知パターンを網羅する一方、設計や認可の文脈に踏み込んだ判断は苦手です。

codepatrolは「巡回(patrol)」という発想でこの問題に向き合います。リポジトリを調査領域に分割し、領域ごとにAIがソースコードを読んでセキュリティ観点を当て、レポートを累積します。複数sessionにまたがる長期作業を前提とし、実行するたびに現状（どの領域を・いつ調査済みか）を確認して続きから進めます。

## このスキルがやること

1. **調査対象リストの生成**: サーバー側のエントリポイント（HTTPルーティング、リアルタイム通信、横断middleware、webhook/バッチ等）を走査し、調査領域リストを自動生成します
2. **観点チェックリストのカスタマイズ**: 汎用の観点マスター（`CHECKLIST.md`）を、対象リポジトリの実装（認可機構・テナント境界・認証方式・DB層等）に合わせて具体化した作業用コピーを生成します
3. **領域ごとの調査**: 未調査の領域を選び、エントリポイントからデータ層までデータフローを追ってチェックリストの各観点を当てます
4. **Codexによる批判的レビュー**: 調査結果をCodexに批判させ、単独調査では見落とす攻撃ベクトルを検出します。このレビューを経ていない調査結果はレポートにしません
5. **レポート出力**: 問題・根拠・深刻度・推奨対応をまとめたレポートを、Cosenseまたはローカルファイルに出力します
6. **再調査**: 調査済みの領域を調査し直した時は、前回の問題との対応と、修正された物、今回検出しなかった物をレポートに書きます
7. **設定ファイルの変更の記録**: 調査で直した調査対象リストと観点チェックリストを、調査用のbranchにcommitします。pushとpull requestの作成は、ユーザーの指示を待ちます
8. **トリアージ**: レポートの問題を、修正に必要な仕様の判断の重さで3つの修正難度に分け、どれを誰が直すかを決める材料にします
9. **状態同期**: 問題を直すpull requestの状態を、トリアージのページとレポートに反映します
10. **自動修正**: トリアージした問題を、subagentが1つずつ修正し、pull requestをready for reviewまで仕上げます。どの深刻度と修正難度の問題を自動修正するか、mergeまで自動で進めるかは、起動時に選びます
11. **Release PR deploy note**: 自動修正で積み上がったpull requestの概要欄から、デプロイの前後に人間がやる事をまとめ、release pull requestのコメントに投稿します

観点チェックリスト（`CHECKLIST.md`）は認可・トークン・認証・SSRF・XSS・インジェクション・ファイル・DoS・情報漏洩・ビジネスロジック・設定の11カテゴリを収録していますが、これは出発点であり網羅的ではありません。リストにないパターンも積極的に調査し、見つけた観点は報告します。

## 含まれるスキル

- **codepatrol**: 調査を指揮します。ユーザーが起動するのはこのskillだけです。調査する領域が1つの時も、このskillから始めます
- **codepatrol-setup**: 調査対象リストと観点チェックリストを生成・更新します。codepatrolが起動したsubagentが実行します
- **codepatrol-report**: 1つの領域を調査してレポートを出力します。codepatrolが起動したsubagentが実行します
- **codepatrol-triage**: レポートの問題を分類し、トリアージのページを作ります。codepatrolが起動したsubagentが実行します
- **codepatrol-sync-state**: 問題を直すpull requestの状態を、ページに反映します。codepatrolが起動したsubagentが実行します
- **codepatrol-autofix**: 1つの問題を修正し、pull requestを仕上げます。codepatrolが起動したsubagentが実行します
- **codepatrol-autofix-deploynote**: デプロイの前後に人間がやる事を、release pull requestのコメントにまとめます。codepatrolが起動したsubagentが実行します

## 前提条件

- **subagentを起動できる環境**: 調査はsubagentが行います
- **主エージェントのmodel**: Claude CodeのOpus以上のtier（Opus、Fable、Mythos等）で実行します。Sonnet・Haiku等の下位tierでは問題の検出能力が足りないため、リストの生成・調査・レポート出力のいずれもせずに停止します
  - 下位tierで作られた既存の調査対象リスト・観点チェックリストを見つけた場合は、調査の前にOpus以上で作り直します
- **`codex-consultation` スキル**: リストのレビューと、調査結果の批判的レビューで使用します。リストの生成・更新と調査には必須で、他のスキルや主エージェント自身のレビューで代替しません
  - CodexのmodelはSol以上のflagship（Sol、Astra等）、reasoning effortはmedium以上に設定しておきます。Luna・Terra等の軽量tierやSolより前の世代のmodelでは停止します
  - Codexがusage limitや通信障害で停止した場合は、リストのレビューもレポートの出力もせずに中断します
- **Cosense書き出しを選ぶ場合**: [cosense CLI](https://www.npmjs.com/package/@helpfeel/cosense-cli)（`npm install -g @helpfeel/cosense-cli`）のインストールとログイン、および [Cosense操作用のskill](https://github.com/helpfeel/cosense-cli) が別途必要です
  - ローカルファイル出力だけを使う場合、これらは不要です
  - トリアージ、状態同期、自動修正は、Cosense書き出しの場合だけ使えます
- **`kuden` plugin**: 指揮役が `kuden:orchestrator` の心得に従います。subagentの報告の確かめ方、中断後の再開、利用上限に合わせた速度の調整が、ここで決まります
- **`software-factory-mode` plugin**: 自動修正に必須です。修正するsubagentが、この開発フローに従ってpull requestを作ります。依存する `sanity-review`、`conversation-context` も必要です
  - 調査、トリアージ、状態同期だけを使う場合、これらは使われません
  - conversation-context はレポートのヘッダー形式の背景知識でもあります

## 使い方

```bash
/codepatrol:codepatrol [未調査の領域だけ | 全領域 | 領域名... | 調査対象リストを更新しろ | トリアージ | 状態同期 | 自動修正]
```

- 引数なしで実行すると、現状（調査済み・未調査の領域）を確認し、調査する範囲を尋ねます
- `調査対象リストを更新しろ` と指示すると、リポジトリを再走査して調査対象リストを最新化します
- `トリアージ` と指示すると、レポートの問題を分類したページを作ります
- `状態同期` と指示すると、問題を直すpull requestの状態をページに反映します
- `自動修正` と指示すると、トリアージした問題をsubagentが修正していきます。最初は、深刻度Highの修正難度1から始める事を勧めます。仕様変更の意思決定がほぼ無い問題です
  - 修正のpull requestは人間の確認を待たずに作られます。pull requestの概要欄、対話コンテキスト、sanity-reviewの報告書、Release PR deploy noteで、後からまとめて確認できます
- 1領域の調査には数十分かかり、多くのtokenを消費します
- 調査はgit worktreeで行うので、調査の間も自分のcheckoutで他の作業を続けられます

初回実行時は、書き出し先（Cosense/ローカル）の確認、調査対象リストの生成、観点チェックリストのカスタマイズをセットアップします。生成物は利用者リポジトリの `.dev/codepatrol/` に保存され、以降のsessionで再利用されます。

## インストール方法

マーケットプレイスを登録してから、pluginをインストールします。

```bash
/plugin marketplace add shokai/agent-skills
/plugin install codepatrol
```

既にマーケットプレイスを登録済みの場合、新しいスキルをインストールするには [マーケットプレイスの更新](../../README.md#スキルをうまくインストールできない場合) が必要です。

他のpluginは [リポジトリルートのREADME](../../README.md) から探せます。
