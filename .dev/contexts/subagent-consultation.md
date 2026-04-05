# subagent-consultation 対話コンテキスト

- PR: #14
- Branch: `subagent-consultation`
- Source commit: 37a8778
- Updated at: 2026-04-04 23:45:00

## 目的

codex-consultationの効果の源泉が「批判的思考の連鎖（プロトコルによる精度向上）」か「セカンドオピニオン（異なるモデルへの相談による網羅性向上）」かを切り分けるため、Opus↔Opusで相談するsubagent-consultationスキルを作成する。

## 設計方針

- codex-consultation版のSKILL.mdを正として、subagent-consultation版の文面を可能な限り揃える。実行手段固有の差分のみ許容する
  - codex版にのみ存在するセクション: インストール確認、CLIオプション詳細、タイムアウト、「出力が大きすぎる場合」
  - これらはcodex exec固有の内容なので、subagent版に対応物がないことを許容する
- エラー検知セクションはcodex版の「コマンド失敗」に対して「実行失敗」という表現に揃えた。兆候の例や対処手順の構成はcodex版と同一にした
- marketplace.jsonとREADME.mdはcodex-consultationエントリと同じ構成・文体で追加した
- README.mdの前提行はsubagentには不要なため省略した。前提がない場合は書かない方針

## 却下した代替案

- セクション番号を揃えるためにsubagent版にダミーのセクション1（「Agentツールは常に利用可能なのでスキップ」）を追加する案。ユーザーが些末と判断し不採用
- codex-consultation側を修正して両方を変える案。ユーザーがcodex版には手を加えないと明言し不採用
- セクション番号を廃止してタイトルベースの参照にする案（subagentの提案）。ユーザーの方針に合わないため不採用
- README.mdにsubagent版の前提行（「前提: なし」等）を追加する案。Codexが指摘したが、前提がない場合は書く必要なしとユーザーが判断

## 意図的に対応しないこと

- codex版にある「出力が大きすぎる場合」セクションのsubagent版への追加。今回のスコープは文面の差分解消であり、新セクション追加は別の判断
- セクション番号のずれの解消。インストール確認セクションの有無による必然的なずれ

## 発見された制約

- なし

## 新たに確認できた事実

- codex-consultationの効果にはA（批判的思考の連鎖）とB（セカンドオピニオン）の2要因がある。Haiku↔Codexの実験で、相談元Agentの知性が低いと相談先の性能も引き出せないことが判明している
- subagent-consultationはAだけでも十分効くかの検証を意図している
- Codexによるmarketplace.json/README.mdレビューで、フィールド構成・description文体・plugin.jsonとの整合性はすべて問題なしと確認された

## 注意が必要な難所

- subagent版の修正時、ユーザーはcodex版を正としてsubagent版だけを修正するアプローチを求めている。codex版に手を加えようとすると拒否される
- SKILL.mdの差分修正時、1箇所ずつユーザーに比較を見せて許可を取るフローが求められた。一括修正やWriteでの全面書き換えは拒否される

## 残作業

- なし
