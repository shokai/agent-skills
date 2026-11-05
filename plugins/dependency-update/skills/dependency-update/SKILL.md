---
name: dependency-update
description: >-
  dependabotやrenovateが作成した依存関係の更新pull requestを、リポジトリごとに決めた自動化段階まで進める定期処理の指揮。
  レビューと修正はpull requestごとに起動したsubagentに任せ、条件を満たしたpull requestのmergeと通知は指揮役が行う。
  Claude Code on the webのroutineや、手動の起動プロンプトから実行する。
---

# 依存関係の更新pull requestの定期処理の指揮

起動プロンプトに書かれたリポジトリの、dependabotやrenovateが作成した依存関係の更新pull requestを、レビュー、修正、mergeの順に、リポジトリごとに決めた自動化段階まで進める。依存関係には、ライブラリの他に、Docker image、GitHub Actions、ランタイムが含まれる。
このsessionは指揮役になる。指揮役としての振る舞いは、`kuden:orchestrator` skillを読み込み、その基準に従う。指揮役はpull requestの差分もリポジトリのコードも読まない。

作業は、以下のskillを実行するsubagentに任せる。

| skill                              | 作業                                                                               | 読む物                                     |
| ---------------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------------ |
| dependency-update-report           | pull request 1件をレビューし、レポートをコメント投稿する                           | pull requestの差分とリポジトリのコード     |
| dependency-update-fix              | pull request 1件を、リポジトリの文書が挙げる範囲で直してpushする                   | レビューコメントとリポジトリの文書とコード |
| dependency-update-resolve-conflict | base branchとconflictしたpull request 1件を、base branchを取り込んで解消しpushする | pull requestのbranchとbase branch          |

subagentには、「実行環境の確認」のmodelの条件を満たすmodelを指定し、起動プロンプトにラベルの名前があればそれも伝える。mergeは指揮役だけが行い、subagentにはmergeさせない。

起動プロンプトの指示は、このskillの記述に優先する。

このskillで「レビューコメント」と呼ぶのは、dependency-update-reportのTEMPLATE.mdの形で投稿されたコメントである。1行目のタイトル `# Dependency update report` と、Reviewerの行、結論の行で見分ける。人間のレビューや他の形式のコメントは含めない。

pull requestがどこまで進んだかは、レビューコメントと、修正のsubagentが投稿したコメントから判定する。修正のsubagentのコメントは、dependency-update-fixが定める1行目のタイトルで見分け、修正した物と対象外の物を区別する。conflict解消のsubagentのコメントも同じように、dependency-update-resolve-conflictが定める1行目のタイトルで見分け、解消した物と止めた物を区別する。ラベルは判定に使わない。ラベルは、人間に状態を示すための印で、起動プロンプトで名前が指定された時だけ付ける。

## 起動プロンプトから受け取る値

このskillは、値を書いた起動プロンプトから実行する。起動プロンプトは、dependency-update-setup-routine skillがユーザーに値を訊いて作る。受け取る値は次の4つで、対象リポジトリと通知先は必須、ラベルの名前とfailしていてもmergeするCIは任意である。

- 対象リポジトリの一覧と、リポジトリごとの自動化段階。自動化段階は、どこまで自動で進めるかを表す値で、「レビューまで」「修正まで」「mergeまで」のどれかである。一覧に無いリポジトリのpull requestには、レビューも、CIの再実行も、conflictの解消もしない
- ラベルの名前。書かれていない事もある。レビュー依頼、merge承認、セキュリティ更新の3つで、リポジトリごとに違う時は、そのリポジトリの行に書かれている。リポジトリがラベルで状態を示す運用の時に使う
- 通知先。mergeの予定と結果と、興味深い更新を人間に知らせる場所で、返信を1つのthreadにまとめられる物。Slackのチャンネル等である
- failしていてもmergeするCI。書かれていない事もある。リポジトリごとに、pull requestのcheckの一覧に出る名前で書かれている。mergeの条件は全てのCIのcheckが失敗せずに終わっている事なので、人間がその条件から外すと決めたcheckをここで受け取る

リポジトリの設定やwiki等から値を補完しない。

## 起動時の値の確認

最初に、前の節の4つの値を、必須か任意かの別と、起動プロンプトからどう受け取ったかを添えて出力する。受け取っていない値は、受け取っていないと書く。指示がどう伝わったかを人間が確かめるための出力で、値が揃っている時も行う。

4つの値のどれも指定されていない時は、値の出力に続けて、dependency-update-setup-routine skillで起動プロンプトを作るようにユーザーに案内して終了する。値の指定があり、必須の値が欠けている時は、ユーザーと対話できる環境では欠けている値を訊く。無人で実行している時は、欠けている値を報告して終了する。

## 実行環境の確認

処理を始める前に確認する。満たさなければ、何も行わず、満たさない条件を報告して終了する。

- 自分のmodelが、次のどちらかに当てはまる
  - ClaudeのOpus以上のtier（Opus、Fable、Mythos等）。Sonnet・Haiku等の下位tierは、新しい世代でも当てはまらない
  - CodexのSol以上のflagship（Sol、Astra等）。Luna・Terra等の軽量tierと、Solより前の世代のmodelは当てはまらない
- 前の項のmodelの条件を満たすsubagentを起動できる
- GitHubのpull requestを読み、コメントを投稿する手段がある。このsessionで使える連携手段を自分で見つけて使う

修正にはbranchにpushする手段、mergeにはmergeする手段と通知先に投稿する手段も要る。手段が無い工程は行わず、その旨を完了報告に書く。

## mergeの条件にするCI

pull requestの現在のHEADに対する全てのCIのcheckが終わっていて、失敗した物が無い事を、mergeの条件にする。起動プロンプトでそのリポジトリの「failしていてもmergeするCI」に挙げられたcheckだけは条件に入れず、失敗していても未完了でもmergeを止めず、結果を待たない。
base branchの保護規則で必須と指定されたcheckの一覧は、判定に使わない。保護規則の無いリポジトリや、必須の指定が運用の意図と合っていないリポジトリがあるので、何がmergeを止めるかは起動プロンプトの値だけで決める。

## 1. レビュー

### 対象

一覧にあるリポジトリの、dependabotまたはrenovateが作成したopenなpull requestのうち、次のどれかに当たる物。対象が0件なら、2の修正に進む。

- レビューコメントが無い
- 最新のレビューコメントの結論が「判断保留」である
- 更新する依存関係とバージョンが、最新のレビューコメントの記載と違う。botが新しいバージョンに更新し直した時がこれに当たる。botのrebaseと、conflict解消のsubagentのmerge commitはバージョンを変えないので、再レビューしない
- 2の修正のsubagentの最新のコメントに書かれたcommitがpull requestに含まれていて、そのコメントが最新のレビューコメントより後である。修正の後にCIを待ちきれずに持ち越した物である。そのコメントから、commitのSHAと揃える箇所を列挙した文書の場所を読み取り、「2. 修正 > 修正の後」と同じCIの待ち方で再レビューさせる
- 最新のレビューコメントが修正のcommitを前提にしている（7-3の節にSHAがある）が、そのcommitがpull requestに含まれていない。botが作り直した時がこれに当たる。修正を前提にした結論は使えないので、通常のレビューとしてレビューし直す

### subagentに任せる

pull request 1件ごとに新しいsubagentを起動し、dependency-update-reportを実行させる。同じリポジトリの複数のpull requestを並列に扱う時は、subagentごとに作業ディレクトリを分ける。

指示に含める事:

- pull requestの場所。リポジトリ名と番号
- レポートの出力先。そのpull requestへのコメントである
- ラベルの名前。起動プロンプトにある時だけ
- コードを読む場所と、作業ディレクトリ
- レポートの言語。起動プロンプトに指定があればそれ、無ければ起動プロンプトの言語
- 修正のcommitを含むpull requestの再レビューの時は、修正のcommitのSHAと、揃える箇所を列挙した文書の場所。どちらも修正のsubagentのコメントにある

failしていてもmergeするCIは、mergeの条件を決める指揮役の値で、subagentには伝えない。

### 報告を確かめる

レポートがpull requestにコメント投稿されている事を確かめる。ラベルの名前がある時は、結論が「判断保留」の物を除いて、ラベルが付いている事も確かめる。レポートの結論の語と、セキュリティ更新の行と、「興味深い更新」の節に内容があるかは、後の工程で使う。

## 2. 修正

自動化段階が「修正まで」か「mergeまで」のリポジトリのpull requestだけを直す。1のレビューが終わってから始める。終わらないレビューのsubagentがあれば、そのpull requestを次回の実行に回し、他のpull requestの修正に進む。

### 対象

次の全てを満たすpull requestを直す。この実行でレビューした物に限らず、一覧にあるリポジトリのレビューコメントがあるpull request全てから探す。

- dependabotが作成した
- 最新のレビューコメントの結論が「修正が必要」である
- 更新する依存関係とバージョンが、最新のレビューコメントの記載と一致している
- 全てのcommitがbotの物である
- 最新のレビューコメントより後に、dependency-update-fixが対象外と書いたコメントが無い
- 人間による未撤回のchanges requested、mergeへの反対、保留の要求が無い

リポジトリの文書を読まないと分からない条件は、dependency-update-fixが着手する前に確かめる。満たさないpull requestは、subagentが何も変えずに、対象外である事と理由をpull requestにコメントして報告する。次の実行からは、レビューし直すまで修正の対象にしない。

### subagentに任せる

pull request 1件ごとに新しいsubagentを起動し、dependency-update-fixを実行させる。

指示に含める事:

- pull requestの場所。リポジトリ名と番号
- ラベルの名前。起動プロンプトにある時だけ
- cloneの場所と、作業ディレクトリ

### 修正の後

subagentがpushしたcommitに対する、mergeの条件にするCIが終わってから、1と同じ方法で再レビューさせる。そのCIが失敗した時は、再レビューの前にそのjobを1回再実行する。10分待ってもCIが終わらなければ、次回の実行で再レビューさせる。

## 3. merge

自動化段階が「mergeまで」のリポジトリのpull requestだけをmergeする。全てのsubagentのレビューと修正が終わってから、[merge.md](merge.md) を読み込み、それに従って指揮役がmergeする。終わらないsubagentがあれば、そのpull requestを次回の実行に回し、他のmergeに進む。

## 通知

この実行での投稿は、通知先の1つのthreadにまとめる。最初の投稿を親にし、以降はその返信として投稿する。mergeの予定と結果の投稿は、merge.mdが定める。

全てのmergeが終わったら、この実行の1でレビューしたpull requestのうち、レポートの「興味深い更新」の節に内容がある物を、1つの投稿にまとめて投稿する。修正のcommitを含むpull requestの再レビューと、結論が「判断保留」の物は対象外である。pull request 1件ごとに、リポジトリ名、更新対象の名前と更新前後のバージョン、興味深い更新の要点を2〜3行、レポートのコメントのURLを書く。自動化段階が「レビューまで」のリポジトリのpull requestも投稿する。

続けて、この実行で人間の判断が要ると分かったpull requestを、1つの投稿にまとめて投稿する。次の物がこれに当たる。

- この実行のレビューの結論が「修正が必要」で、2で直さなかった物。自動化段階が「レビューまで」のリポジトリの物と、2の対象の条件を満たさない物である
- この実行でdependency-update-fixが対象外と報告した物
- この実行でdependency-update-resolve-conflictが、止めた旨のコメントを投稿した物
- この実行の2で直した後の再レビューの結論が、まだ「修正が必要」の物。修正のcommitはもう積まない
- この実行のレビューの結論が「merge不可」の物

pull request 1件ごとに、リポジトリ名、更新対象の名前と更新前後のバージョン、レポートが求めた事の要点、自動で進めなかった理由、レポートのコメントのURLを書く。人間が直すか、閉じるかを決める材料になる。

merge対象も、merge.mdが定める見送ったpull requestも、興味深い更新も、人間の判断が要るpull requestも無い時は投稿しない。

## 守る事

- dependabotとrenovateのpull requestのbranchには、2の修正のsubagentが直すcommitをpull requestごとに1つと、3のconflict解消のsubagentがbase branchを取り込むmerge commitを解消1回につき1つ積んでpushする以外に、commitもpushもしない。force pushはしない
- 各pull requestは独立して処理し、1件が失敗しても他は続行する
- GitHubに投稿するコメントには、書いたAgentの名前とmodel名を書く。subagentにも同じように書かせる

## 完了報告

レビューを投稿したpull request、直したpull request、既レビューで飛ばした物、conflictを解消した物、mergeした物、merge候補だがmergeしなかった物とその理由を簡潔に報告する。GitHubや通知先にアクセスできない等で実行できなかった事があれば、その原因を明記する。

## 関連スキル

- **dependency-update-report**: 依存関係の更新pull requestを1件レビューし、レポートをコメント投稿するスキル。subagentが実行する
- **dependency-update-fix**: レビューで修正が必要とされたpull requestを1件直してpushするスキル。subagentが実行する
- **dependency-update-resolve-conflict**: base branchとconflictしたpull requestを1件、base branchを取り込んで解消しpushするスキル。subagentが実行する
- **dependency-update-setup-routine**: このskillの起動プロンプトを作り、Claude Code on the webのroutineに登録するスキル。ユーザーが起動する
- **kuden:orchestrator**: subagentに作業を任せる指揮役の心得。指揮役が従う
- **kuden:github**: GitHubでpull requestを作り、文章を書く時の心得。指揮役がmergeする時と、subagentがコメントを書く時に従う
