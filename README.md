# Claude Code Agent Skills by shokai

shokai's Claude Code Agent Skillsのコレクションです。

## 概要

このリポジトリは、Claude Codeで利用できるスキル（プラグイン）を配布するためのマーケットプレイスです。
開発作業を効率化するための様々なスキルを提供しています。

## インストール方法

### 1. マーケットプレイスを登録

```bash
/plugin marketplace add shokai/claude-skills
```

### 2. スキルをインストール

```bash
/plugin install library-update-review
```

## 含まれるスキル

### library-update-review

ライブラリ更新pull requestのレビューを支援するスキルです。

**機能:**
- dependabotやrenovatebotが作成したPRの分析
- release noteやchangelogの詳細調査
- ライブラリの依存関係の確認
- コードの使用箇所の特定
- 必要に応じたコード更新の提案
- 過去の失敗事例の調査

**使い方:**

```bash
/library-update-review [PR-URL-or-number]
```

dependabotやrenovatebotのPRブランチで実行すると、包括的なレビューレポートを作成します。

## ライセンス

MIT License - Copyright (c) 2026 shokai

詳細は [LICENSE](LICENSE) を参照してください。

## リポジトリ

https://github.com/shokai/claude-skills
