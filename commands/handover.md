Generate a HANDOVER.md summarizing this session.

**Before generating HANDOVER.md, execute the following pre-steps in order:**

## Step 1: /log（セッションログ記録）
Execute the `/log` skill to record agent and skill usage during this session.
- Record all agent invocations (architect, implementer, tester, etc.) and skill executions (team-dev, team-investigation, etc.)
- If no agents or skills were used in this session, note "ログ対象なし" and proceed to Step 3 (skip Step 2)

## Step 2: /inspect（ログ分析・改善提案）
Execute the `/inspect` skill to analyze the recorded logs and generate improvement proposals.
- Analyze failure patterns, inefficiencies, and areas for improvement
- Output proposals to `proposals/` directory if any issues are found
- **Skip this step if Step 1 produced no log entries**

## Step 3: HANDOVER.md 生成
Generate the HANDOVER.md with the following sections:
1. **Completed** — What was accomplished
2. **Decisions** — Key decisions made and their reasoning
3. **Issues & Fixes** — Bugs encountered and how they were resolved
4. **Lessons Learned** — Pitfalls and insights
5. **Next Steps** — What should be done next
6. **Key Files** — Important files created or modified

Write in Japanese. Be concise but thorough.
Save the file as HANDOVER.md in the project root.
