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

- [library-update-review](plugins/library-update-review/README.md) - ライブラリ更新pull requestのレビューを支援する
- [codex-consultation](plugins/codex-consultation/README.md) - Codex CLI（OpenAI）にセカンドオピニオンを求める
- [subagent-consultation](plugins/subagent-consultation/README.md) - Agentツール（subagent）にセカンドオピニオンを求める
- [conversation-context](plugins/conversation-context/README.md) - 対話コンテキストを `.dev/contexts/` にexportし、別セッションやレビューでimportする
- [sanity-review](plugins/sanity-review/README.md) - PRのレビュー報告書を作成し、実装者の正気を疑う
- [prose-proofreading](plugins/prose-proofreading/README.md) - Markdownドキュメントの文章を校正する
- [unconventional-simplification](plugins/unconventional-simplification/README.md) - 定石外発想で実装をシンプルにする
- [codepatrol](plugins/codepatrol/README.md) - リポジトリを領域ごとに巡回してセキュリティ調査する
- [software-factory-mode](plugins/software-factory-mode/README.md) - sessionをSoftware Factory Modeに切り替える
- [kuden](plugins/kuden/README.md) - 作者がAIとの対話の中で伝えてきた心得を集めたガイドライン群
