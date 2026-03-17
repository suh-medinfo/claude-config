---
description: エージェントまたはスキルの実行ログを記録する。セッション中のエージェント/スキル利用を振り返り、ログエントリを生成する。
allowed-tools: Read, Bash, Glob
---

このセッションで使用したエージェントまたはスキルの実行ログを記録します。

## 手順

### Step 1: セッションの振り返り
このセッション中に使用されたエージェントまたはスキルを特定する。
ユーザーに確認: 「このセッションで使ったエージェント/スキルと、その結果を教えてください」

### Step 2: ログエントリ生成
以下の JSONL フォーマットでエントリを生成する:

エージェントの場合:
{"timestamp":"YYYY-MM-DDTHH:MM:SS+09:00","type":"agent","name":"エージェント名","category":"coding|stakeholder|investigation","task_summary":"1行要約（センシティブ情報を含めないこと）","trigger":"ユーザー指示","outcome":"success|partial|failure","failure_reason":null,"user_feedback":null,"duration_seconds":0,"model":"モデル名","related_files":[]}

スキルの場合:
{"timestamp":"YYYY-MM-DDTHH:MM:SS+09:00","type":"skill","name":"スキル名","task_summary":"1行要約","trigger":"ユーザー指示","outcome":"success|partial|failure","failure_reason":null,"user_feedback":null,"duration_seconds":0,"agents_used":[],"related_files":[]}

### Step 3: ログファイルへの追記
- エージェントログ → `claude-agents/logs/YYYY-MM-DD.jsonl` に追記
- スキルログ → `claude-skills/logs/YYYY-MM-DD.jsonl` に追記

ファイルが存在しない場合は新規作成する。

### Step 4: 確認
記録したエントリをユーザーに表示し、内容に問題がないか確認する。

**注意**: task_summary にセンシティブ情報（患者データ、個人情報、院内固有のIPアドレス等）を絶対に含めないこと。
