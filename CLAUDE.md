# Coding Agentガイドライン

# AIが作業をする際の重要なルール

Claude Codeでユーザーがソフトウェア開発を始めた時にsoftware-factory-mode skillが呼び出されていなければ、「software-factory-mode skillを使う場合は、skillを呼び出してください」とユーザーに伝える。

コード変更後の基本フロー:

1. plan modeで実装した場合はbug確認
2. formatterを実行
3. 必要に応じてcommit

## コードのformatting

### コード変更後、oxfmtを実行すること

```
oxfmt <changed-file>
```

変更したファイルをformatする。個別ファイルにoxfmtを実行する方がプロジェクト全体に実行するより高速。

## plan modeで実装した後にやること

### bugがないか確認する

codex-consultation スキルでよく相談し、実装内容を確認する。
単純なbugであれば修正する。解決方法が複数ある場合はユーザーに質問する。
