# conversation-context

対話コンテキストのexport/importを行うAgent Skillです。2つのスキルがセットでインストールされます。

会話で共有された目的・意図・設計判断・制約条件を `.dev/contexts/` ディレクトリに書き出し、別セッションやレビューで読み込むことができます。1対多のコンテキスト共有により、複数の子PRをまとめた親PRのレビューやコードの自動改善に便利です。

## 含まれるスキル

- **conversation-context-export**: 現在の会話コンテキスト（目的、設計判断、制約条件など）を `.dev/contexts/` に書き出す。PRが存在する場合はPRコメントにも投稿する
- **conversation-context-import**: `.dev/contexts/` に保存されたコンテキストを読み込み、現在のセッションに反映する

## 前提条件

- exportでのPRの検出とPRコメントへの投稿には、GitHub CLI（`gh`）のインストールと認証が必要。importでは使わない

## 使い方

```bash
/conversation-context-export
/conversation-context-import
```

## インストール方法

マーケットプレイスを登録してから、pluginをインストールします。

```bash
/plugin marketplace add shokai/agent-skills
/plugin install conversation-context
```

既にマーケットプレイスを登録済みの場合、新しいスキルをインストールするには [マーケットプレイスの更新](../../README.md#スキルをうまくインストールできない場合) が必要です。

他のpluginは [リポジトリルートのREADME](../../README.md) から探せます。
