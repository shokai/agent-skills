# dependency-update

dependabotやrenovateが作成した依存関係の更新pull requestを、リポジトリごとに決めた自動化段階までレビュー・修正・mergeするAgent Skillです。起動したsessionが指揮役になり、pull requestごとに起動したsubagentがレビューと修正を行い、条件を満たしたpull requestを指揮役がmergeして通知します。Claude Code on the webのroutineに登録して毎日動かす事を想定しています。

## 背景

依存関係の更新pull requestは毎日届きます。ライブラリだけでなく、Docker image、GitHub Actions、ランタイムの更新も含まれます。1件ずつrelease noteを読み、アプリケーションへの影響を調べ、CIを待ってmergeする作業は、人間が続けるには量が多すぎます。一方で、何も読まずに自動mergeすると、breaking changeやバージョンの不整合を取り込みます。

dependency-updateは、1件ごとにAIがレビューレポートを書いてpull requestに残し、レポートの結論に基づいて修正とmergeを進めます。どこまで自動で進めるかはリポジトリごとに決め、レビューの出来を人間が見ながら徐々に自動化段階を引き上げます。

## このスキルがやること

1. **レビュー**: 対象リポジトリの未レビューの、dependabotやrenovateが作ったpull request 1件ごとにsubagentを起動し、更新対象の概要、バージョン、変更スコープ、依存関係、使用箇所、release note、コード・設定の修正の要否、過去の失敗を調べたレポートをpull requestにコメント投稿します。レポートの冒頭に「修正無しでmerge可」等の結論を固定の語で書きます。pull requestがどこまで進んだかは、このレポートの有無と内容で判定するので、ラベルで状態を管理しないリポジトリでも使えます。ラベルの名前を起動プロンプトに書いた時は、人間向けの印としてレビュー依頼のラベルを付け、セキュリティ更新と判定した時はそのラベルも付けます
2. **修正**: 結論が「修正が必要」のdependabotのpull requestを、リポジトリのルール文書が参照する「依存関係の更新時に揃える箇所」の文書の範囲で、subagentが直してpushします。範囲外の修正が要る物は直しません。修正後はCIを待って再レビューします
3. **merge**: 結論が「修正無しでmerge可」で、CIが通り、conflictも人間の反対も無いpull requestを、指揮役が1件ずつmergeします。CIは全てのcheckが失敗せずに終わっている事を条件にし、GitHubの保護規則の必須の指定は読みません。failしていてもmergeしてよいcheckは、起動プロンプトにリポジトリごとに書きます。セキュリティ更新を先にmergeし、状態表現にラベルを使っているリポジトリではmerge後にmerge承認のラベルを付けます。先にmergeした物とconflictしたpull requestは、botが自分でrebaseするのを数分待ちます。subagentのcommitが積まれていてbotがrebaseしない物は、subagentがbase branchを取り込んで解消してpushし、CIを待ってmergeします
4. **通知**: mergeの予定と結果、mergeしなかった物とその理由、レビューしたpull requestの興味深い更新、修正が必要だが自動では直さなかった等、人間の判断が要るpull requestを、通知先の1つのthreadにまとめて投稿します
5. **routineの登録**: 起動プロンプトを値を訊いて作り、Claude Code on the webのroutineを作成・更新します

## 含まれるスキル

- **dependency-update**: 定期処理を指揮します。routineや手動の起動プロンプトから実行します
- **dependency-update-setup-routine**: 起動プロンプトを作り、routineに登録します。ユーザーが手動で起動します
- **dependency-update-report**: 依存関係の更新1件をレビューし、レポートを出力します。dependency-updateが起動したsubagentが、pull requestにコメント投稿します。1件の更新を手元でレビューする時は、ユーザーが直接実行します
- **dependency-update-fix**: pull request 1件を、リポジトリの文書が挙げる範囲で直してpushします。dependency-updateが起動したsubagentが実行します
- **dependency-update-resolve-conflict**: base branchとconflictしたpull request 1件を、base branchを取り込んで解消してpushします。dependency-updateが起動したsubagentが実行します

## 前提条件

- **主エージェントのmodel**: ClaudeのOpus以上のtier（Opus、Fable、Mythos等）か、CodexのSol以上のflagship（Sol、Astra等）でreasoning effortがmedium以上のmodelで実行します。条件に当てはまらないmodelでは、何もせずに停止します
- **subagentを起動できる環境**: レビュー、修正、conflictの解消はsubagentが行います。subagentも同じ条件のmodelで動かします
- **GitHubにアクセスする手段**: pull requestを読み、コメントを投稿する手段が要ります。修正とconflictの解消にはbranchにpushする手段、mergeにはmergeする手段も要ります。手段が無い工程は行いません
- **通知先に投稿する手段**: Slackのチャンネルのように、返信を1つのthreadにまとめられる場所です。投稿する手段が無い時は、mergeをしません
- **`kuden` plugin**: 指揮役が `kuden:orchestrator` の心得に従い、mergeとコメントの投稿で `kuden:github` の心得に従います
- **routineの実行環境**: このpluginと `kuden` pluginをinstallしておきます

## 使い方

### routineに登録する

```bash
/dependency-update:dependency-update-setup-routine
```

対象リポジトリと自動化段階、ラベルの名前、failしていてもmergeするCI、通知先を質問し、起動プロンプトを作ります。ラベルは任意で、リポジトリがラベルで状態を示す運用の時だけ書きます。failしていてもmergeするCIも任意で、mergeを止めなくてよいと決めたcheckがある時だけ書きます。Claude Code on the webのroutineを扱える環境では、routineの作成か、既存のroutineの更新まで行います。`起動プロンプトだけ` を付けて実行すると、起動プロンプトと設定項目を出力するだけにし、登録は人間が行います。

起動プロンプトは、skill名と値だけを書いた短い物です。

```
dependency-update skillを実行する。

対象リポジトリと自動化段階:
- example-org/app-server: mergeまで
- example-org/app-cli: mergeまで
- example-org/app-web: レビューまで

ラベル: レビュー依頼は `please review`、merge承認は `ship it`、セキュリティ更新は `security`
failしていてもmergeするCI: example-org/app-serverの `docker-build`
通知先: Slackの #example-channel
```

自動化段階は、どこまで自動で進めるかを表す値で、「レビューまで」「修正まで」「mergeまで」の3つです。初めて対象にするリポジトリは「レビューまで」から始め、レビューの出来を見ながら徐々に上げる事を勧めます。起動プロンプトに書いた指示はskillに優先するので、待ち時間のような例外はここに書けます。

### 手動で実行する

同じ起動プロンプトを手元のsessionに渡すと、その場で1回実行します。routineに登録した後の初回は、手動で実行して様子を見る事を勧めます。
指揮役は最初に、起動プロンプトから受け取った値を必須・任意の別と共に出力するので、指示が伝わったかをそこで確かめられます。値を指定せずにskillだけを起動すると、処理は始めず、`dependency-update-setup-routine` で起動プロンプトを作るように案内します。

### 1件の更新を手元でレビューする

```bash
/dependency-update:dependency-update-report [レビュー対象] [レポートの出力先]
```

レビュー対象には、pull requestのURLや番号の他、まだpull requestになっていないbranchや作業ツリーの変更も指定できます。dependabotやrenovateが作った物に限らず、人間が作った更新もレビューします。出力先には、pull requestへのコメント、ファイル、sessionへの表示等を指定します。どちらかを指定しなかった時は、調査を始める前に訊かれます。

行うのはレビューだけで、コードは直しません。修正が要る時は、レポートを読んでから同じsessionで頼めます。routine、subagent、通知先は要りません。pull requestにコメント投稿したレポートは、routineが投稿したレポートと同じように扱われ、その結論で修正やmergeが進みます。

## インストール方法

マーケットプレイスを登録してから、pluginをインストールします。

```bash
/plugin marketplace add shokai/agent-skills
/plugin install dependency-update
```

依存する [kuden](../kuden/README.md) も一緒にインストールされます。

既にマーケットプレイスを登録済みの場合、新しいスキルをインストールするには [マーケットプレイスの更新](../../README.md#スキルをうまくインストールできない場合) が必要です。

他のpluginは [リポジトリルートのREADME](../../README.md) から探せます。
