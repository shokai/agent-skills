# dependency-update

dependabotやrenovateが作成した依存関係の更新pull requestを、リポジトリごとに決めた段階までレビュー・修正・mergeするAgent Skillです。起動したsessionが指揮役になり、pull requestごとに起動したsubagentがレビューと修正を行い、条件を満たしたpull requestを指揮役がmergeして通知します。Claude Code on the webのroutineに登録して毎日動かす事を想定しています。

## 背景

依存関係の更新pull requestは毎日届きます。ライブラリだけでなく、Docker image、GitHub Actions、ランタイムの更新も含まれます。1件ずつrelease noteを読み、アプリケーションへの影響を調べ、CIを待ってmergeする作業は、人間が続けるには量が多すぎます。一方で、何も読まずに自動mergeすると、breaking changeやバージョンの不整合を取り込みます。

dependency-updateは、1件ごとにAIがレビューレポートを書いてpull requestに残し、レポートの結論に基づいて修正とmergeを進めます。どこまで自動で進めるかはリポジトリごとに決め、レビューの出来を人間が見てから段階を上げます。

## このスキルがやること

1. **レビュー**: 対象リポジトリの未レビューのpull request 1件ごとにsubagentを起動し、更新対象の概要、バージョン、変更スコープ、依存関係、使用箇所、release note、コード・設定の修正の要否、過去の失敗を調べたレポートをpull requestにコメント投稿します。レポートの冒頭に「修正無しでmerge可」等の結論を固定の語で書きます。pull requestがどこまで進んだかは、このレポートの有無と内容で判定するので、ラベルで状態を管理しないリポジトリでも使えます。ラベルの名前を起動プロンプトに書いた時は、人間向けの印としてレビュー依頼のラベルを付け、セキュリティ更新と判定した時はそのラベルも付けます
2. **修正**: 結論が「修正が必要」のdependabotのpull requestを、リポジトリのルール文書が参照する「依存関係の更新時に揃える箇所」の文書の範囲で、subagentが直してpushします。範囲外の修正が要る物は直しません。修正後はCIを待って再レビューします
3. **merge**: 結論が「修正無しでmerge可」で、必須のCIが通り、conflictも人間の反対も無いpull requestを、指揮役が1件ずつmergeします。セキュリティ更新を先にmergeし、ラベルを使う時はmerge後にmerge承認のラベルを付けます
4. **通知**: mergeの予定と結果、mergeしなかった物とその理由、レビューしたpull requestの興味深い更新、修正が必要だが自動では直さなかった等、人間の判断が要るpull requestを、通知先の1つのthreadにまとめて投稿します
5. **routineの登録**: 起動プロンプトを値を訊いて作り、Claude Code on the webのroutineを作成・更新します

## 含まれるスキル

- **dependency-update**: 定期処理を指揮します。routineや手動の起動プロンプトから実行します
- **dependency-update-setup-routine**: 起動プロンプトを作り、routineに登録します。ユーザーが手動で起動します
- **dependency-update-report**: pull request 1件をレビューし、レポートをコメント投稿します。dependency-updateが起動したsubagentが実行します
- **dependency-update-fix**: pull request 1件を、リポジトリの文書が挙げる範囲で直してpushします。dependency-updateが起動したsubagentが実行します

## 前提条件

- **主エージェントのmodel**: Claude CodeのOpus以上のtier（Opus、Fable、Mythos等）で実行します。Sonnet・Haiku等の下位tierでは、何もせずに停止します
- **subagentを起動できる環境**: レビューと修正はsubagentが行います。subagentもOpus以上のtierで動かします
- **GitHubにアクセスする手段**: pull requestを読み、コメントを投稿する手段が要ります。修正の段階にはbranchにpushする手段、mergeの段階にはmergeする手段も要ります。手段が無い段階は行いません
- **通知先に投稿する手段**: Slackのチャンネルのように、返信を1つのthreadにまとめられる場所です。投稿する手段が無い時は、mergeをしません
- **`kuden` plugin**: 指揮役が `kuden:orchestrator` の心得に従い、mergeとコメントの投稿で `kuden:github` の心得に従います
- **routineの実行環境**: このpluginと `kuden` pluginをinstallしておきます

## 使い方

### routineに登録する

```bash
/dependency-update:dependency-update-setup-routine
```

対象リポジトリと段階、ラベルの名前、通知先を質問し、起動プロンプトを作ります。ラベルは任意で、リポジトリがラベルで状態を示す運用の時だけ書きます。Claude Code on the webのroutineを扱える環境では、routineの作成か、既存のroutineの更新まで行います。`起動プロンプトだけ` を付けて実行すると、起動プロンプトと設定項目を出力するだけにし、登録は人間が行います。

起動プロンプトは、skill名と値だけを書いた短い物です。

```
dependency-update skillを実行する。

対象リポジトリと段階:
- example-org/app-server: mergeまで
- example-org/app-cli: mergeまで
- example-org/app-web: レビューまで

ラベル: レビュー依頼は `please review`、merge承認は `ship it`、セキュリティ更新は `security`
通知先: Slackの #example-channel
```

段階は「レビューまで」「修正まで」「mergeまで」の3つです。初めて対象にするリポジトリは「レビューまで」から始め、レビューの出来を見てから上げる事を勧めます。起動プロンプトに書いた指示はskillに優先するので、待ち時間のような例外はここに書けます。

### 手動で実行する

同じ起動プロンプトを手元のsessionに渡すと、その場で1回実行します。routineに登録した後の初回は、手動で実行して様子を見る事を勧めます。

## インストール方法

マーケットプレイスを登録してから、pluginをインストールします。

```bash
/plugin marketplace add shokai/agent-skills
/plugin install dependency-update
```

依存する [kuden](../kuden/README.md) も一緒にインストールされます。

既にマーケットプレイスを登録済みの場合、新しいスキルをインストールするには [マーケットプレイスの更新](../../README.md#スキルをうまくインストールできない場合) が必要です。

他のpluginは [リポジトリルートのREADME](../../README.md) から探せます。
