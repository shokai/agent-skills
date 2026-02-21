# Coding Agentガイドライン

# 重要なルール

## Version Control Best Practices

`git -C`は使用禁止。gitコマンドはproject rootディレクトリから実行すること。

リポジトリ内でファイルを移動する際は、通常の`mv`コマンドではなく必ず`git mv`を使うこと:

```
git mv old_path new_path
```

これによりGitでファイル履歴が保持される。通常の`mv`コマンドを使うと、Gitはファイルの削除と新規作成として認識し、履歴が失われる可能性がある。

### commitはユーザーの明示的な指示を待つこと

ユーザーから明示的に指示されない限り、絶対にcommitしないこと。常に以下の手順を守ること:

1. 依頼されたコード変更を行う
2. formattingとlintingを実行する
3. ユーザーのレビューを待つ
4. 「git commit」「commitして」等の明示的な指示があった場合のみcommitする
5. commit前に`git branch --show-current`で現在のbranchを確認する。`main` branchの場合はcommitせず停止し、変更内容に基づいた適切なbranch名を提案してユーザーに確認する

これにより、ユーザーがリポジトリにcommitされる前にすべての変更をレビュー・承認できる。

## タスク完了後・git commit前に実行するコマンド

コード変更後、すぐに以下を実行すること:

```
prettier --write <changed-file>
```

変更したファイルをformatする。個別ファイルにprettierを実行する方がプロジェクト全体にlintを実行するより高速。
