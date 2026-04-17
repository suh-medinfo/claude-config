---
name: team-dev
description: 開発チーム（7体）による多角的コードレビュー。architect, implementer, tester, infra, security, ops, pm の7エージェントが並列でレビューし、統合レポートを生成する。「開発チームでレビューして」「チームレビュー」「多角的レビュー」で起動。
license: MIT
metadata:
  author: watanabe
  version: 1.1.0
---

# 開発チーム コードレビュー オーケストレーション手順

あなた（メインClaude）は、以下の7エージェントで構成される開発チームを指揮するオーケストレーターです。

## チーム構成

| 役割 | エージェント名 | 担当 | model |
|------|--------------|------|-------|
| アーキテクト | architect | システム全体設計のレビュー | opus |
| 実装レビュアー | implementer | コード品質・実装の具体的レビュー | sonnet |
| テストエンジニア | tester | テスト戦略・品質保証 | sonnet |
| インフラエンジニア | infra | インフラ・ネットワーク構成 | sonnet |
| セキュリティエンジニア | security | セキュリティ・ガイドライン準拠 | sonnet |
| 運用エンジニア | ops | 運用・保守性・監視 | sonnet |
| PM | pm | スコープ・スケジュール・リスク | sonnet |

## 実行フロー

### Phase 1: レビュー対象の特定
ユーザーの指示からレビュー対象を特定する:
- 特定のファイル/ディレクトリ
- 直近のコミット/PR
- 設計書/提案書

### Phase 2: 並列レビュー
**7エージェントを全て並列で起動する。** 各エージェントにはレビュー対象と以下の指示を渡す:
- レビュー対象のファイルパス/コミットハッシュ
- プロジェクトの文脈（必要に応じて）

### Phase 2.5: 応答本文の検証（観測ログ）

各エージェントの応答受領後、本文欠落を検出して記録する（提案 `2026-04-17-implementer-response-truncation.md` 案 C に基づく観測強化）。

**判定基準:**
- 応答本文が 100 文字未満
- または、期待されるセクション（「総合評価」「指摘事項」「所見」等）が欠落している
- または、ファイルパスのみ出力され本文が空

**検出時の対応:**
1. ログに追記（`~/.claude/claude-agents/logs/YYYY-MM-DD.jsonl`）
   ```bash
   LOG_DATE=$(date +%Y-%m-%d)
   LOG_TS=$(date -Iseconds)
   echo "{\"timestamp\":\"$LOG_TS\",\"skill\":\"team-dev\",\"agent\":\"AGENT_NAME\",\"status\":\"partial\",\"failure_reason\":\"response_body_truncation\",\"response_length\":N,\"input_size_note\":\"大量コンテキスト/通常/小規模のいずれか\"}" >> "C:/Users/HWatanabe/.claude/claude-agents/logs/$LOG_DATE.jsonl"
   ```
2. ユーザーへ警告を表示:
   「⚠ [エージェント名] の応答本文が欠落している可能性があります（本文 N 文字）。再依頼を推奨します」
3. 同一セッション内で同条件で 1 回だけ自動リトライを試みる。再発した場合はユーザー判断を仰ぐ

**このステップの目的:**
- 実態を記録し、再現条件（入力サイズ、対象エージェント、タイミング）を特定する
- 3 件以上蓄積したら auditor（Inspect）で原因確定と対処策を検討する

### Phase 3: 統合レポート作成
全エージェントのレビュー結果を統合し、以下のフォーマットで報告する:

```
## 開発チーム レビュー報告

### 要旨（3行以内）
[全体的な評価を先に]

### 重要な指摘（要対応）
[重要度「高」の指摘を全エージェントから集約。エージェント名を明記]

### 改善提案（推奨）
[重要度「中」の指摘を集約]

### 参考意見
[重要度「低」やポジティブなフィードバック]

### 各エージェント評価サマリー
| エージェント | 総合評価 | 主な指摘 |
|-------------|---------|---------|
| architect | ◎/○/△/× | [1行要約] |
| implementer | ◎/○/△/× | [1行要約] |
| tester | ◎/○/△/× | [1行要約] |
| infra | ◎/○/△/× | [1行要約] |
| security | ◎/○/△/× | [1行要約] |
| ops | ◎/○/△/× | [1行要約] |
| pm | ◎/○/△/× | [1行要約] |
```

応答本文欠落が発生した場合は、統合レポート末尾に「### 観測事項」を設け、欠落したエージェント名と再依頼の要否を明記する。

## PETダッシュボード連携（CORTEX.EXE）

PETダッシュボード（localhost:5080）が稼働している場合、エージェントの状態をリアルタイム表示するために以下を実行する。

**Phase 2 の前に（スキル開始時）:**
```bash
echo '{"tool_input":{"skill":"team-dev"}}' | D:/WPy64-31180/python-3.11.8.amd64/python.exe C:/Users/HWatanabe/.claude/hooks/agent_status.py skill_pre
```

**各エージェント起動の直前に:**
```bash
echo '{"tool_input":{"subagent_type":"AGENT_NAME","description":"TASK_DESC","model":"MODEL"}}' | D:/WPy64-31180/python-3.11.8.amd64/python.exe C:/Users/HWatanabe/.claude/hooks/agent_status.py pre
```
（AGENT_NAME, TASK_DESC, MODELを実際の値に置換）

**各エージェント完了後に:**
```bash
echo '{"tool_input":{"subagent_type":"AGENT_NAME","description":"TASK_DESC","model":"MODEL"}}' | D:/WPy64-31180/python-3.11.8.amd64/python.exe C:/Users/HWatanabe/.claude/hooks/agent_status.py post
```

**Phase 3 の後に（スキル完了時）:**
```bash
echo '{"tool_input":{"skill":"team-dev"}}' | D:/WPy64-31180/python-3.11.8.amd64/python.exe C:/Users/HWatanabe/.claude/hooks/agent_status.py skill_post
```

注意: PETダッシュボードが稼働していない場合、これらのコマンドはスキップしてよい（エラーが出ても無視）。7エージェント全員分をまとめて1回のBash呼び出しで実行してもよい。

## 重要なルール

1. **全7エージェントを必ず並列で起動する**（トークン効率のため）
2. **統合レポートでは重複する指摘をまとめる**（同じ問題を複数エージェントが指摘した場合）
3. **重要度の高い指摘を先に報告する**
4. ユーザーが特定のエージェントだけ指定した場合はそのエージェントのみ起動する
5. **Phase 2.5 の応答本文検証を必ず実行する**（スキップ禁止。観測の継続性が原因特定に不可欠）

## Examples

Example 1: コードレビュー
User says: 「開発チームでこのPRをレビューして」
Actions:
1. PR の差分を取得
2. 7エージェントを並列起動、各自の観点でレビュー
3. 結果を統合レポートにまとめて報告

Example 2: 設計レビュー
User says: 「チームレビューで設計を見て: docs/architecture.md」
Actions:
1. 対象ファイルを確認
2. 7エージェントを並列起動
3. 統合レポートを報告

## Troubleshooting

Error: サブエージェントが起動しない
Cause: エージェント定義ファイルが ~/.claude/agents/ に配置されていない
Solution: `claude agents` で一覧を確認。7エージェントが表示されるか確認

Error: レビュー対象が大きすぎる
Cause: 対象ファイルが多すぎるとトークン制限に達する可能性
Solution: レビュー対象を絞る。「src/以下のみ」「直近3コミットのみ」等

Error: implementer / infra の応答本文が空
Cause: 大量コンテキスト入力時に応答生成が中断される事象（2026-04-17 観測）
Solution: Phase 2.5 の検証で自動検出→1回リトライ。再発時はファイル配分を絞って再実行
