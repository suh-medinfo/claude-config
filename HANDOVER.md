# HANDOVER

## Completed
- `common.md` の Session Handover セクションに「セッション開始時の HANDOVER.md 読み込みルール」を追加
- デプロイ済みの `~/.claude/CLAUDE.md` にも同じルールを即時反映

## Decisions
- **common.md とデプロイ済み CLAUDE.md の両方を同時編集**: common.md だけ変更すると再デプロイまで反映されないため、両ファイルを同時に更新した
- **Session Handover セクションの先頭に配置**: セッション開始時のルールなので、終了時ルール（`/handover` 提案）より前に記載

## Issues & Fixes
| 問題 | 原因 | 対処 |
|------|------|------|
| 特になし | — | — |

## Lessons Learned
- グローバルルール変更時は、ソース（`claude-md/common.md`）と デプロイ先（`~/.claude/CLAUDE.md`）の両方を更新する必要がある
- `deploy.ps1` を再実行すればソースから再生成されるが、即時反映したい場合は手動で両方編集するのが確実

## Next Steps
- [ ] `deploy.ps1` の `$ScriptDir` 解決ロジック改善 or bash版デプロイスクリプト追加（前回からの継続課題）
- [ ] 変更をコミット＆プッシュ

## Key Files
| ファイル | 状態 |
|----------|------|
| `claude-md/common.md` | 更新（HANDOVER.md 読み込みルール追加） |
| `~/.claude/CLAUDE.md` | 更新（同上を即時反映） |
| `deploy.ps1` | 既存（未変更） |
