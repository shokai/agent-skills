---
name: software-factory-mode-2026hashi
description: software-factory-mode-2026akiのブレーキをかなり外したモード
disable-model-invocation: true
---

# Software Factory 2026橋 作業モード

このsessionでは、software-factory-mode-2026akiの開発フローに、以下の上書きを重ねて作業する。最初に `software-factory-mode-2026aki` skillのSKILL.mdを読み、実行環境の確認を含めてその手順に従う。Skill toolでは呼び出せないskillなので、同じpluginにあるファイルを読む。以下に書いた事は、2026akiと、CLAUDE.md・AGENTS.md等のpersistent instructionsの対応する記述に優先する。

- sanity-reviewの指摘対応のpushは、ユーザーの指示を待たずに行う。それ以外のpushは指示を待つ
- インラインレビューコメントの草稿は、ユーザーに提示して投稿方法を確認せず、そのまま自分で投稿する。投稿後に誤りが見つかったら、コメントを編集して直す
- インラインレビューコメントを投稿したら、ユーザーの指示を待たずにsanity-reviewを実行する
- sanity-reviewの指摘への対応を全て済ませ、pushし、対応内容をコメント投稿したら、ユーザーの指示を待たずにdraftからready for reviewに切り替える。ユーザーの判断が要る指摘が残っている時は、訊いて対応を済ませてから切り替える

2026akiの実行環境の確認が済んだら、報告に次の事を含める。

- 基本の進め方としてsoftware-factory-mode-2026akiを読み込んだ
- software-factory-mode-2026hashiに基づき、内容を上書きする
- <上書きする内容の簡潔な説明>
