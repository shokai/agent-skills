---
name: remote-session
description: >-
  Claude Codeのremote session(claude --rc)をtmux内に新しく起動し、そのURLをユーザーに伝えるスキル。
  ユーザーが「新規claude --rcを起動して」「〇〇のclaude session作って」「remote claude接続を作って」と言った時に使用する。
allowed-tools: Bash(${CLAUDE_SKILL_DIR}/scripts/tmux-claude-rc:*)
---

# claude remote sessionの新規起動

`${CLAUDE_SKILL_DIR}/scripts/tmux-claude-rc` で起動し、出力されたremote sessionのURLをユーザーに伝える。

## 起動するディレクトリの決め方

過去にclaudeを使ったディレクトリは `~/.claude/projects/` のディレクトリ名から分かる。ディレクトリ名はpathの記号を `-` に置き換えたもので、元のpathを一意には復元できない。実在を確かめてからpathを決める。

## tmux sessionの選び方

既存のtmux sessionにwindowとして足すか、新規のtmux sessionを作るかを、AskUserQuestionでユーザーに確認する。既存sessionの選択肢には、起動するディレクトリで作業しているsessionを挙げる。

- 既存sessionに足す: `${CLAUDE_SKILL_DIR}/scripts/tmux-claude-rc -w <ディレクトリパス> <session名>`
- 新規sessionを作る: `${CLAUDE_SKILL_DIR}/scripts/tmux-claude-rc <ディレクトリパス> <session名>`。session名は指示の内容に合わせて簡潔に決める。指示に作業内容が無ければproject名にする
