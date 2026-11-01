# mergeの手順

段階が「mergeまで」のリポジトリのpull requestを、指揮役が自分でmergeする。mergeする時は、`kuden:github` skillを読み込み、その基準に従う。

## 対象

段階が「mergeまで」のリポジトリのopenなpull requestのうち、最新のレビューコメントの結論が「修正無しでmerge可」の物がmergeの候補である。この実行でレビューした物に限らない。候補のうち、次の全てを満たす物をmergeする。

- 全てのcommitが、botの物か、修正のsubagentがコメントにSHAを書いたcommitで、更新する依存関係とバージョンがレビューコメントの記載と一致している
- レビューコメントが修正のcommitを前提にしている（7-3の節にSHAがある）なら、そのcommitがpull requestに含まれている
- 現在のHEADに対する、mergeに必須のCIが全て成功している
- base branchとconflictしていない
- 人間による未撤回のchanges requested、mergeへの反対、保留の要求が無い

## 条件を満たさないpull requestの扱い

- base branchとconflictしている物はmergeせず、次回の実行で判定し直す。作り直しやrebaseの依頼は、全てのcommitがbotの物か修正のsubagentのcommitである時だけ行う。人間のcommitがある物には何も依頼しない。dependabotが作成し、修正のcommitを含む時は、dependabotにpull requestを作り直させる。それ以外は、botが対応する方法でrebaseを依頼する。rebase後の再レビューは要らない
- mergeに必須のCIが失敗している物は、失敗した必須のjobを1回再実行し、結果が出るのを待ってから判定する。5分待っても終わらなければ、次回の実行で判定し直す。この実行の修正の後に再実行済みのjobは、再実行しない

## 順序

レポートがセキュリティ更新としている物を先にmergeし、その後は1件ずつmergeする。1件mergeする度に、次のpull requestがmergeの条件を全て満たすかを確かめ直す。merge方法はリポジトリの設定に従う。mergeするのは条件を確かめたHEADで、mergeの直前にHEADが変わっていれば、そのpull requestはmergeせずに次回の実行で判定し直す。

起動プロンプトにラベルの名前がある時は、mergeしたpull requestのレビュー依頼のラベルを外してmerge承認のラベルを付ける。

## 通知

投稿は、指揮役のskillが定める1つのthreadにまとめる。

- merge対象が1件以上ある時は、mergeを始める前に、これからmergeするpull requestの一覧をリポジトリ名・タイトル・URL付きで投稿する。投稿に失敗した時は1件もmergeせず、その旨を完了報告に書く
- 1件mergeする度に、その結果を投稿する。投稿に失敗した時は、残りのmergeを止めて、その旨を完了報告に書く
- 全てのmergeが終わったら、候補のうちmergeしなかった物を、その理由付きで投稿する。作り直しやrebaseを依頼した物も含める。mergeした物が0件でも投稿する。結論が「修正が必要」や「merge不可」の物は候補ではなく、指揮役のskillの「通知」が人間の判断が要る物として扱う
