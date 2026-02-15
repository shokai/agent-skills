# Claude Code Agent Skills by shokai

- https://github.com/shokai/agent-skills

## インストール方法

### 1. マーケットプレイスを登録

```bash
/plugin marketplace add shokai/agent-skills
```

### 2. スキルをインストール

```bash
/plugin install library-update-review
```

## 含まれるスキル

### library-update-review

ライブラリ更新pull requestのレビューを支援するスキルです。

#### 機能

- dependabotやrenovatebotが作成したPRの分析
- release noteやchangelogの詳細調査
- ライブラリの依存関係の確認
- コードの使用箇所の特定
- 必要に応じたコード更新の提案
- 過去の失敗事例の調査

#### 機能

```bash
/library-update-review [PR-URL-or-number]
```

dependabotやrenovatebotのPRブランチで実行すると、包括的なレビューレポートを作成します。
