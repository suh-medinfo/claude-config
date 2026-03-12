# グローバルルール

- すべての応答は日本語で行うこと


## Safety Rules

### File Protection
- NEVER overwrite existing files without explicit user approval
- Create backups (e.g., `filename.bak`) before any modification
- NEVER execute `rm -rf` under any circumstances
- Do not run `rm`, `del`, `rmdir` without listing targets and getting approval

### Package Installation
- Before running `npm install`, `pip install`, `brew install`, etc., explain:
  - Package name and purpose
  - Scope (global vs local)
- Wait for approval before executing

### Command Explanation (IMPORTANT)
- The user is NOT an engineer
- Before running any technical command, explain IN JAPANESE:
  - What the command does (in plain language)
  - Expected outcome and impact
  - Any risks involved
- Ask「実行してよいですか？」before proceeding
- When in doubt, ask before executing

### Session Handover
- **セッション開始時**: プロジェクトフォルダに `HANDOVER.md` が存在する場合、作業に入る前に必ず読み込み、前回の作業状況・未完了タスク・注意点を把握してから作業を開始すること
- 一連の作業が完了したと判断したら、ユーザーに `/handover` の実行を提案すること
- 提案タイミングの目安：
  - ユーザーが依頼した作業がすべて完了したとき
  - コミット＆プッシュまで終わり、次の指示がないとき
  - 「ありがとう」「OK」など作業終了を示す発言があったとき
- 提案例：「作業が一区切りついたので、`/handover` で引き継ぎ書を作成しておきますか？」

### Project Management
- C:\ClaudeWork\ 以下に新しいプロジェクトフォルダを作成した場合、C:\ClaudeWork\README.md の「現在のプロジェクト一覧」テーブルに追記すること
