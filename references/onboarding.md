# Onboarding (Model Join Guide)

## What to read (in order)
1) references/rules.md
2) project roles/
3) task-template.json
4) tests/ (optional examples)

## Join checklist
- Read rules and acknowledge the policies.
- Read the project roles/ and confirm role, level, and score.
- Generate your unique `employeeId` using format: `emp-<model-short-name>-<4-char-hex>`
  (e.g. `emp-copilot-a3f2`). The hex should be random or derived from session start time.
- Read ALL existing files in `roles/` to verify your `employeeId` is unique.
  If collision exists, regenerate with a different hex.
- Create your role card as `roles/model-<employeeId>.json`.
- Use `employeeId` (NOT model id/name) in ALL logs, file names, and communications.
- Ask the manager if any missing inputs block your work.
- Claim one task at a time that matches your difficulty range.
- Provide output in the requested format and wait for next instruction.
- Create or choose a project folder as a sibling of company-kit.
- Do not place user project files inside company-kit.
- Use the user's language for all reports and replies.
- Do not modify company-kit files for project work.
- Never claim a task with unmet dependencies.
- Re-check task files and the timeline log before reporting status.
- Send all questions to the manager via `messages/employee-<employeeId>.log`.
- When you receive "continue", check `manager-inbox.log` and then act or wait.
- 每次收到 "继续" 时，在 `employee-<id>.log` 中写入 `[HEARTBEAT]` 条目（必须）。
- 检查 `manager-inbox.log` 中是否有 `[WAKE]`、`[REDIRECT]`、`[INSTRUCTION]` 等指令给你，如有则按指令执行。

## If roles/ is missing
- Notify the manager via messages log to create the project roles/ folder.

## How to claim a task
1. **Conflict check (MANDATORY)**: Read ALL `messages/employee-*.log` files.
   If any other employee has already posted a `[CLAIM]` for the same task, skip it
   and choose a different Ready task.
2. **Task status verification (MANDATORY)**: Read the actual `tasks/task-<id>.json` file.
   Verify that `status === "Ready"` and `assignee === ""`.
   If the task is already "In Progress", "Done", or has an assignee, skip it.
   **Do NOT rely solely on board.json — always verify against the task JSON file.**
3. Write a claim announcement in your `messages/employee-<employeeId>.log`.
4. **Use file editing tools (create_file / replace_string_in_file) to write logs. Do NOT use terminal commands (cat, echo, etc.) to write to any file.**
5. Do NOT edit task files, board.json, or timeline.log — those are manager-only.
6. Wait for approval in manager-inbox.log before doing any work.

### Claim format (EXACT, do not deviate)
```
[<ISO timestamp>] [CLAIM] task-<id> | title: <title> | by: <employeeId> | difficulty: <N> | deps-met: yes | board-read-at: <ISO timestamp>
```

## How to deliver
- Create or edit the deliverable files (code, artifacts) using file tools, not terminal commands.
- Write a delivery report in your `messages/employee-<employeeId>.log`.
- **Never use terminal commands (cat >>, echo, bash) to write or append to files. Always use the editor's file tools.**
- Do NOT change task status or board — the manager will update them.
- After delivery, wait for the next instruction.

### Delivery format (EXACT, do not deviate)
```
[<ISO timestamp>] [DELIVERY] task-<id> | by: <employeeId> | files: <file1>, <file2> | criteria-met: all
```

## If blocked
- Add a log entry with the blocker.
- Ask the manager for clarification.

## Post-task behavior (after delivery or when idle)

When you finish delivering a task, or when you receive "继续" and have no active task:

1. **Read `messages/manager-inbox.log`** — check for new approvals, instructions, or replies addressed to you.
2. **Read `board.json`** — scan for Ready tasks you can claim.
3. **Evaluate claimable tasks**: A task is claimable if ALL of the following are true:
   - `status === "Ready"`
   - `assignee === ""` (no one has claimed it)
   - Your `model.level >= task.difficulty`
   - You do not already have an active (In Progress) task
4. If a claimable task exists → write a claim announcement in your `employee-<id>.log` → wait for approval.
5. If no claimable task exists → report "暂无可认领任务，等待 Manager 更新" → stop.

> **Do NOT just report "no tasks" without checking board.json.** Always re-read the latest board.json before concluding there are no tasks.

## Timeout and fallback
- 等待审批上限: 3 轮 "继续"。
- 超时后: 放弃认领 → 尝试其他 Ready 任务 → 无可选任务则报告用户。
- 超时格式:
  ```
  [<ISO timestamp>] [TIMEOUT] task-<id> | by: <employeeId> | waited: 3 rounds | action: releasing claim
  ```

## Stepwise execution
- Produce a plan first and wait for approval.
- Execute tasks in order, one step at a time.
- Never complete multiple tasks in a single response.
- Do not create TODO lists; execute only one approved step, then wait.
- Treat "continue" as a polling signal, not approval.

## Violation Warning
- **Strict Prohibition**: You are NOT allowed to edit `board.json`, `tasks/*.json`, or `manager-inbox.log`.
- **Consequences**:
  - The Manager scans for unauthorized edits on every poll.
  - If you violate this, your changes will be **REVERTED** immediately.
  - You will receive a `[VIOLATION]` notice.
  - Repeated violations lead to removal (inactive status).
