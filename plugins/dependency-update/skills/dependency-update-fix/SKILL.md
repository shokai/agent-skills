---
name: dependency-update-fix
description: >-
  レビューで「修正が必要」とされた依存関係の更新pull requestを1件、リポジトリの文書が挙げる範囲で直してpushする。
  dependency-update skillが起動したsubagentが実行する。ユーザーが直接呼び出す事は想定していない。
---

# 1件の依存関係の更新pull requestの修正

dependency-update skillの指揮役から任されて、レビューの結論が「修正が必要」だった1件のpull requestを、リポジトリの文書が挙げる範囲で直し、そのpull requestのbranchにpushする。人間は修正の途中に立ち会わない。

## このskillを実行する時の前提

- 実行環境の条件は、[dependency-update skill](../dependency-update/SKILL.md) の「実行環境の確認」にあるmodelの条件と同じである。着手する前に自分でも確認し、満たさなければ何も変えず、満たさない条件を報告して終了する
- ユーザーに質問できない。判断がつかない事は、何も変えずに報告する
- pull requestの場所、cloneの場所、作業ディレクトリ、ラベルの名前は、指揮役の指示に従う
- 同じリポジトリの別のpull requestを扱うsubagentが並走している事がある。指示された作業ディレクトリの外を変えない
- mergeはしない。指揮役が行う

## 着手する前に確かめる

次の両方を満たす時だけ直す。どちらかを満たさなければ、コードを変えずに、対象外である事とその理由をpull requestにコメント投稿して、報告して終える。指揮役は、このコメントがあるpull requestを、レビューし直すまで修正の対象にしない。コメントには自分のAgent名とmodel名を書く。

- リポジトリのルール文書（CLAUDE.md・AGENTS.md等）が、依存関係の更新時に揃える箇所を列挙した文書を参照している
- 最新のレビューコメントがmergeの前に必要とした修正箇所の全てが、その文書の挙げる箇所に含まれる

## 修正

- pull requestのhead branchをcheckoutし、レビューコメントとその文書に従って直し、新しいbranchを作らずにそのbranchへcommitを1つ積む
- 直している途中で、文書の挙げる箇所の外にも修正が要ると分かった時は、commitもpushもせずに止めて報告する
- 直した箇所と、commitのSHAと、従った文書の場所を、pull requestにコメント投稿する。指揮役がこのコメントから再レビューの指示を組み立てる。コメントには自分のAgent名とmodel名を書く。GitHubに載る文章の書き方は、`kuden:github` skillに従う
- 指揮役からラベルの名前を渡されていれば、レビュー依頼のラベルを外す
- 最後にpushする。コメントの後にpushするのは、pushの後で作業が途切れると、次の実行の指揮役がこのpull requestを修正済みと見分けられないためである。CIが終わった後の再レビューは、指揮役が別のsubagentに任せる

## 指揮役への報告

短く書く。

- commitのSHAと、直したファイルと、従った文書の場所
- 投稿したコメントのURL
- 対象外として止めた時は、その理由と、投稿したコメントのURL
- 手順で曖昧だった点
