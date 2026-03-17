# 自己改善ループ（Observe → Inspect → Amend → Evaluate）

## 概要

エージェントとスキルの実行を記録・分析し、継続的に改善するためのフレームワーク。

## 1. Observe（記録）

エージェント/スキルの実行ごとに、以下のフォーマットでログを記録する。

### エージェントログ（claude-agents/logs/ に記録）

```jsonl
{
  "timestamp": "2026-03-17T10:30:00+09:00",
  "type": "agent",
  "name": "implementer",
  "category": "coding",
  "task_summary": "Pythonスクリプトのコードレビュー",
  "trigger": "ユーザー指示",
  "outcome": "success",
  "failure_reason": null,
  "user_feedback": null,
  "duration_seconds": 45,
  "model": "claude-sonnet-4-20250514",
  "related_files": ["coding/implementer.md"]
}
```

### スキルログ（claude-skills/logs/ に記録）

```jsonl
{
  "timestamp": "2026-03-17T10:30:00+09:00",
  "type": "skill",
  "name": "team-dev",
  "task_summary": "PRのチームレビュー",
  "trigger": "ユーザー指示",
  "outcome": "success",
  "failure_reason": null,
  "user_feedback": "有用だった",
  "duration_seconds": 120,
  "agents_used": ["architect", "implementer", "tester", "infra", "security", "ops", "pm"],
  "related_files": ["skills/team-dev/SKILL.md"]
}
```

### フィールド説明

| フィールド | 型 | 説明 |
|-----------|-----|------|
| timestamp | string | ISO 8601 形式（JST） |
| type | string | "agent" or "skill" |
| name | string | エージェント名 or スキル名 |
| category | string | "coding" / "stakeholder" / "investigation"（agentの場合のみ） |
| task_summary | string | 実行したタスクの1行要約 |
| trigger | string | 起動のきっかけ |
| outcome | string | "success" / "partial" / "failure" |
| failure_reason | string? | 失敗時の理由 |
| user_feedback | string? | ユーザーからのフィードバック |
| duration_seconds | number | 実行時間（秒） |
| model | string | 使用したモデル（agentの場合） |
| agents_used | string[] | 使用したエージェント一覧（skillの場合） |
| related_files | string[] | 関連する定義ファイル |

## 2. Inspect（分析）

auditor がログを分析し、失敗傾向を検出する。

### トリガー
- 直近10回の実行中、3回以上 failure
- ユーザーからの明示的な指示
- 週次レビュー

### 分析観点
- 特定のエージェント/スキルの失敗率
- 失敗パターン（同じ failure_reason の繰り返し）
- ユーザーフィードバックの傾向

## 3. Amend（修正提案）

auditor が proposals/ に改善提案を出力する。

### 提案フォーマット

```markdown
# 改善提案: [対象名]

## 問題
[検出された問題の説明]

## 根拠
[ログデータに基づく分析結果]

## 提案内容
[具体的な修正案]

## 期待効果
[改善により期待される効果]

## リスク
[修正に伴うリスク]
```

**重要: 人間がレビューするまで本体は変更しない。**

## 4. Evaluate（評価）

提案が採用された場合、最低3回の実行結果で効果を判断する。

### 判断基準
- 改善あり: failure率の低下、ユーザーフィードバックの改善
- 改善なし: 変化なし or 新規 failure の発生 → git revert を検討

## 注意事項

- ログにセンシティブ情報（患者データ等）を絶対に含めない
- proposals/ は必ず人間がレビューしてからマージ
- 自動マージは当面導入しない
- auditor に Bash 権限は付与しない（読み取り専門）
