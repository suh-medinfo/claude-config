# HANDOVER

## 1. 完了した作業

### deploy.ps1 にエージェントデプロイ機能を追加

- **deploy.ps1 改修**: Profile が `core` 以外（`dev`/`full`）の場合、`C:\ClaudeWork\tools\claude-agents` リポジトリから `coding/`, `stakeholder/`, `investigation/` 内の `.md` ファイルを `~/.claude/agents/` にコピーするステップ7を追加
- **Profile ゲーティング**: `core` の場合は `[SKIP]` 表示でエージェントコピーをスキップ
- **結果表示更新**: サマリーに Agents 行を追加（18 files）
- **デプロイ実行**: `deploy.ps1 -Machine laptop -Profile dev` で全資材を配置完了（コマンド6、スキル7、フック1、エージェント18）
- **コミット＆push済み**: `d73cdd8` suh-medinfo/claude-config master

## 2. 判断事項

- **エージェントソースの固定パス**: `C:\ClaudeWork\tools\claude-agents` を直接参照。パスが存在しない場合は `Test-Path` でスキップされる安全設計
- **フラットコピー**: 3ディレクトリ（coding/stakeholder/investigation）内の `.md` を `~/.claude/agents/` に平坦にコピー。サブディレクトリ構造は維持しない（ファイル名の重複がないため）
- **ステップ番号**: 既存のステップ7（sessions/）を8に、ステップ8（結果表示）を9にリナンバリング

## 3. 問題と対応

- 特になし。変更は単純で、dev/core 両方のプロファイルで期待通り動作確認済み

## 4. 学んだこと

- deploy.ps1 のステップ番号はコメントで管理しているため、処理追加時は後続ステップの番号更新を忘れずに

## 5. 次のステップ

- [ ] Profile `full` 時の追加動作の検討・実装（現在は dev と同じ動作）
- [ ] explain-risk.js と risk-guard.py の責務整理
- [ ] `improvement-loop-implementation.md`, `claude-config-fix-v2.md` の削除（作業完了済み）
- [ ] `deploy.ps1.bak` の削除

## 6. 主要ファイル

| ファイル | 状態 |
|---------|------|
| `deploy.ps1` | 更新（エージェントデプロイ機能追加、Profile ゲーティング） |
