# mergeの手順

段階が「mergeまで」のリポジトリのpull requestを、指揮役が自分でmergeする。mergeする時は、`kuden:github` skillを読み込み、その基準に従う。

## 対象

次の全てを満たすpull requestをmergeする。この実行でレビューした物と、過去の実行でレビュー済みのレビュー依頼ラベル付きの物の両方から探す。

- 最新のレビューコメントの結論が「修正無しでmerge可」である。結論がこの語で書かれていない古いレポートのpull requestは対象外
- 全てのcommitが、botの物か、修正のsubagentがコメントにSHAを書いたcommitで、更新する依存関係とバージョンがレビューコメントの記載と一致している
- 現在のHEADに対する、mergeに必須のCIが全て成功している
- base branchとconflictしていない
- 人間による未撤回のchanges requested、mergeへの反対、保留の要求が無い

## 条件を満たさないpull requestの扱い

- base branchとconflictしている物はmergeせず、次回の実行で判定し直す。dependabotが作成し、修正のcommitを含み、それ以外のcommitが全てbotの物である時は、レビュー依頼のラベルを外し、dependabotにpull requestを作り直させる。それ以外は、botが対応する方法でrebaseを依頼する。rebase後の再レビューは要らない
- mergeに必須のCIが失敗している物は、失敗した必須のjobを1回再実行し、結果が出るのを待ってから判定する。5分待っても終わらなければ、次回の実行で判定し直す。この実行の修正の後に再実行済みのjobは、再実行しない

## 順序

セキュリティ更新のラベル付きを先にmergeし、その後は1件ずつmergeする。1件mergeする度に、次のpull requestがmergeの条件を全て満たすかを確かめ直す。merge方法はリポジトリの設定に従う。

mergeしたら、そのpull requestのレビュー依頼のラベルを外してmerge承認のラベルを付ける。

## 通知

投稿は、指揮役のskillが定める1つのthreadにまとめる。

- merge対象が1件以上ある時は、mergeを始める前に、これからmergeするpull requestの一覧をリポジトリ名・タイトル・URL付きで投稿する。投稿に失敗した時は1件もmergeせず、その旨を完了報告に書く
- 1件mergeする度に、その結果を投稿する。投稿に失敗した時は、残りのmergeを止めて、その旨を完了報告に書く
- 全てのmergeが終わったら、この実行でmergeの条件を確かめたpull requestのうち、mergeしなかった物を、その理由付きで投稿する。作り直しやrebaseを依頼してラベルを外した物も含める。mergeした物が0件でも投稿する
