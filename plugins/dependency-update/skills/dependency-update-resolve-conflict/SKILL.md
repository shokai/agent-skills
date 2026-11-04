---
name: dependency-update-resolve-conflict
description: >-
  base branchとconflictした依存関係の更新pull requestを1件、base branchを取り込んで解消し、pushする。
  dependency-update skillが起動したsubagentが実行する。ユーザーが直接呼び出す事は想定していない。
---

# 1件の依存関係の更新pull requestのconflictの解消

dependency-update skillの指揮役から任されて、base branchとconflictしていてbotが自分でrebaseしない1件のpull requestを、base branchを取り込むmerge commitで解消し、そのpull requestのbranchにpushする。人間は解消の途中に立ち会わない。

## このskillを実行する時の前提

- 実行環境の条件は、[dependency-update skill](../dependency-update/SKILL.md) の「実行環境の確認」にあるmodelの条件と同じである。着手する前に自分でも確認し、満たさなければ何も変えず、満たさない条件を報告して終了する
- ユーザーに質問できない。判断がつかない時は、「解消」の定める止め方で止める
- pull requestの場所、指揮役が確かめたHEAD、cloneの場所、作業ディレクトリは、指揮役の指示に従う
- 同じリポジトリの別のpull requestを扱うsubagentが並走している事がある。指示された作業ディレクトリの外を変えない
- pull requestのmergeはしない。指揮役が行う

## 着手する前に確かめる

次の全てを満たす時だけ解消する。満たさなければ、何も変えず、コメントも投稿せずに報告して終える。

- pull requestがまだbase branchとconflictしている
- pull requestのHEADが、指揮役が確かめたHEADと同じである。違えば、botが先にrebaseした等でconflictの状況が変わっている
- 人間のcommitが無い。全てのcommitが、botの物か、dependency-update skillのsubagentがコメントにSHAを書いた物である
- 人間による未撤回のchanges requested、mergeへの反対、保留の要求が無い

## 解消

- pull requestのhead branchをcheckoutし、base branchをmergeしてconflictを解消する。既にあるcommitは書き換えない。解消するのは、lock fileとpackage manifestのように機械的に決まるconflictだけである。コードのconflictで、両側の変更をどう合成するかの判断が要る時は止める
- lock fileのconflictは、両側の差分を手で合成しない。base branch側のlock fileを採り、更新対象を解消前のHEADのlock fileにある確定バージョンに更新し直す。package manifestの範囲指定からlock fileを作り直すだけでは、その範囲をbase branch側の古いバージョンも満たす時に、古いバージョンが残る。lock fileだけを更新し直す手段の例: npmなら `npm install --package-lock-only --ignore-scripts`
- 解消したら、commitする前に、base branchに対するpull requestの差分を、解消前のHEADが持っていた差分と比べる。次のどれかに当たる時は止める
  - 更新対象の確定バージョンが解消前と違う
  - 修正のsubagentのcommitの変更が残っていない
  - 解消で加わった変更が、conflictの解消に要った物を超える
  - base branchが更新対象を既にpull requestより新しいバージョンにしている
- 止める時は、commitもpushもせず、止めた理由と、解消を試みたHEADのSHAをpull requestにコメント投稿して報告する。コメントの1行目は `# Dependency update conflict resolution skipped` にする。指揮役がこのSHAを読み、HEADが変わるまで解消を再び試みず、人間の判断が要る物として通知する。コメントには自分のAgent名とmodel名を書く
- 止めなければ、merge commitを1つ積む
- conflictしたファイルと、どう解消したかと、merge commitのSHAを、pull requestにコメント投稿する。1行目は `# Dependency update conflict resolved` にする。指揮役がこの行でコメントを見分け、SHAを読んでmergeの条件を判定する。コメントには自分のAgent名とmodel名を書く。GitHubに載る文章の書き方は、`kuden:github` skillに従う
- 最後にpushする。コメントの後にpushするのは、pushの後で作業が途切れると、次の実行の指揮役がこのmerge commitを誰の物か見分けられないためである。force pushはしない。pushが拒否されたら、botが同時にrebaseした等でbranchが進んでいるので、やり直さず、投稿したコメントにpushされなかった事を追記して報告する
- ラベルは変えない。解消の後に再レビューはしない

## 指揮役への報告

短く書く。

- merge commitのSHAと、conflictしたファイル
- 投稿したコメントのURL
- 解消を始めた後に止めた時は、その理由と、投稿したコメントのURL
- 着手する前の確認で止めた時は、満たさなかった条件
- 手順で曖昧だった点
