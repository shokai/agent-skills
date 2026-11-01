# remote-session

Claude Codeのremote session（Remote Control、`claude --remote-control` の短縮形が `--rc`）をtmux内に新しく起動するAgent Skillです。remote controlで繋いでいるsessionに「〇〇のclaude session作って」と頼むと、指定したディレクトリで新しいsessionを起動し、そのremote sessionのURLを返します。

## 背景

Claude Codeのremote controlは、パソコンで起動したsessionをスマホから操作できます。sessionは起動したディレクトリのプロジェクトで作業するので、出先で別のリポジトリを独立したsessionで扱いたくなった時は、そのディレクトリで起動したsessionが要ります。

このスキルを入れたsessionを1つremote controlで繋いでおけば、そのsessionに頼んで、別のディレクトリの新しいsessionをいくつでも作れます。

## 前提条件

- tmuxがインストールされている事。新しいsessionはtmuxのsessionかwindowとして起動する
- 起動した新しいsessionがremote controlに接続できる事。Claude Codeが `claude --rc` で接続できる状態にしておく
- Claude Codeの設定ディレクトリが既定の `~/.claude` である事。scriptはそこに書かれるremote sessionのIDを読んでURLを組み立てる

## 使い方

作業中に「新規claude --rcを起動して」「〇〇のclaude session作って」「remote claude接続を作って」と伝えると発動します。既存のtmux sessionにwindowとして足すか、新規のtmux sessionを作るかを訊き、起動後にremote sessionのURLを伝えます。

同梱のscript `skills/remote-session/scripts/tmux-claude-rc` は単体で動くので、ターミナルから直接使う事もできます。PATHの通った場所にコピーするか、パスを指定して実行します。

```bash
# 新規のtmux sessionを作って起動する
tmux-claude-rc <ディレクトリパス> [session名]

# 既存のtmux sessionにwindowとして足して起動する
tmux-claude-rc -w <ディレクトリパス> <session名>
```

起動すると、remote sessionのURLを標準出力に出します。tmuxの中から実行した時はそのwindowに切り替え、外から実行した時はそのsessionにattachします。

## インストール方法

マーケットプレイスを登録してから、pluginをインストールします。どのディレクトリのsessionからでも使えるよう、user scopeにインストールします。

```bash
/plugin marketplace add shokai/agent-skills
/plugin install remote-session
```

既にマーケットプレイスを登録済みの場合、新しいスキルをインストールするには [マーケットプレイスの更新](../../README.md#スキルをうまくインストールできない場合) が必要です。

他のpluginは [リポジトリルートのREADME](../../README.md) から探せます。
