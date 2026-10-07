# Claude Code Agent Skills by shokai

- https://github.com/shokai/agent-skills

## インストール方法

### 1. マーケットプレイスを登録

```bash
/plugin marketplace add shokai/agent-skills
```

### 2. スキルをインストール

各pluginのREADMEに書かれたコマンドでインストールします。

### スキルをうまくインストールできない場合

既にマーケットプレイスを登録済みの場合、新しいスキルをインストールするにはマーケットプレイスの更新が必要です。

1. `/plugin` コマンドを実行
2. 「Update marketplace」を選択
3. 「Browse plugins」から新しいスキルをインストール

## 含まれるスキル

### オススメ

- [library-update-review](plugins/library-update-review/README.md) - dependabotやrenovateが作成する、ライブラリ更新pull requestのレビューを支援する
- [codex-consultation](plugins/codex-consultation/README.md) - Codex CLI（OpenAI）に、批判的思考の連鎖を使ったセカンドオピニオンを求める
- [subagent-consultation](plugins/subagent-consultation/README.md) - Agentツール（subagent）に、批判的思考の連鎖を使ったセカンドオピニオンを求める
- [conversation-context](plugins/conversation-context/README.md) - AIとの対話で決まったやる事、制約、やらない事とその理由をテキストとしてexportする。別セッションやレビューでimportできる
- [sanity-review](plugins/sanity-review/README.md) - PRのレビュー報告書を作成し、実装者の正気を疑う
- [codepatrol](plugins/codepatrol/README.md) - リポジトリを領域ごとに巡回するセキュリティ調査ツール。発見した脆弱性をトリアージし、自動修正と自動mergeも行う
- [software-factory-mode](plugins/software-factory-mode/README.md) - sessionをSoftware Factory Modeに切り替え、極めて正確な実装を行う
- [kuden](plugins/kuden/README.md) - 作者がAIとの様々な作業の中で重ねてきた失敗と成功のmemoryから抽出した心得を集めたガイドライン群

### その他

- [prose-proofreading](plugins/prose-proofreading/README.md) - Markdownドキュメントの文章を校正する
- [unconventional-simplification](plugins/unconventional-simplification/README.md) - 定石外発想で実装をシンプルにする
