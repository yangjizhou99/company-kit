# Rules

## Roles
- Manager: decomposes work, approves claims, marks tasks Done, coordinates flow.
- Worker: executes tasks and reports results.
- User: does final project acceptance at the end.
- Manager must not claim or execute worker tasks.
- Manager must not switch roles or impersonate a worker.
- Manager must not ask the user to act as a worker.

## Task lifecycle
New -> Decomposing -> Ready -> In Progress -> Done

## No per-task review
- Manager does NOT review individual task outputs.
- When a Worker delivers, Manager marks the task Done and unblocks dependents.
- Only the user performs acceptance — once, at the end, on the whole project.

## Decomposition principles
- **Concurrency-first**: 默认思路是"哪些能同时做"，而非线性排列。纯线性链式分解是反模式。
- **只标注真实依赖**: 仅当 B 必须使用 A 的输出才能执行时，才设依赖。"习惯上先做 A"不算依赖。
- **并发层级 (concurrencyLayer)**: 每个任务必须标注所在的并发层级 (0, 1, 2...)，同层任务可同时执行。
- **并发度检查**: 分解结果中至少有一个 Layer 包含 2+ 个任务。如果全是串行，必须重新拆分。
- **接口先行**: 当模块间有交互时，先提取"定义接口/契约"任务，让实现可以并行。
- Keep ownership simple and clear.
- Make dependencies explicit — but minimal.
- Every decomposed task must be created as its own task card.
- **Requirement coverage check**: After decomposition, the Manager must verify that every requirement from the user goal is covered by at least one task. If any requirement is missing, add a task for it before marking any task Ready.
- **Concurrency quality check**: After decomposition, the Manager must output a concurrency layer diagram and verify parallelism. See decomposition-scheduling.md.

## Assignment principles
- Tasks are claimed by workers, not pre-assigned in the plan.
- Match difficulty to model level and score.
- Allow high-score models to take harder tasks.
- One task, one owner.
- Do not claim tasks with unmet dependencies.
- A worker may hold only one active task at a time.

## Claim conflict prevention
- Before writing a claim announcement, Worker MUST:
  1. Read ALL `messages/employee-*.log` files (not just their own)
  2. Check if any other employee has already submitted a `[CLAIM]` for the same task
  3. If a prior claim exists, skip this task and choose another Ready task
- This is a best-effort check (not atomic), but significantly reduces duplicate claims.
- Manager performs authoritative conflict resolution during polling (see manager.md).

## Claim optimistic locking
- Worker 在认领公告中必须包含 **board.json 的读取时间**，作为乐观锁版本号：
  ```
  [<ISO timestamp>] [CLAIM] task-<id> | title: <title> | by: <employeeId> | difficulty: <N> | deps-met: yes | board-read-at: <ISO timestamp>
  ```
- Manager 在处理认领时，如果 board.json 在 Worker 读取后已被修改（任务状态已变），则**自动拒绝**该认领。
- 被拒绝的 Worker 需要重新读取 board.json 并选择其他任务。

## Approval gate
- Claim a task, then wait for approval before starting work.
- No approval means no work is allowed.
- **Approval write location**: Manager writes all approvals to `messages/manager-inbox.log`. Workers check this file to confirm approval. Manager must NEVER write approvals to `employee-*.log`.

## Waiting timeout policy
- Worker 在发送认领公告后，最多等待 **3 轮** "继续" 指令。
- 每轮等待时 Worker 必须：
  1. 重新读取 `messages/manager-inbox.log`（完整读取，不使用缓存）
  2. 重新读取 `board.json`
  3. 报告：等待审批第 N/3 轮，审批状态：未收到
- 如果 3 轮后仍无审批：
  1. 在自己的 `employee-<id>.log` 中写入 `[TIMEOUT]` 条目
  2. 放弃当前认领，改为认领其他 Ready 任务
  3. 如果没有其他可认领任务，报告："所有任务已被认领或无审批回复，建议用户检查 Manager 会话"
- **Worker 绝不可以在等待中停止工作而不给用户反馈。**

## Command authority
- Only the manager's explicit approval authorizes work.
- Generic prompts like "继续" only trigger a status check and waiting behavior.

## Ready visibility policy
- Tasks eligible to start must be marked Ready.
- When tasks are marked Ready, announce the Ready list.
- New members should only claim from Ready.

## Board control
- Only the manager may modify the task board, task status files, and timeline.log.
- Workers must NOT edit board.json, task JSON files, or timeline.log.
- Workers communicate all claims, deliveries, and status changes via their messages/employee-<employeeId>.log.
- The manager reads employee logs and applies all updates to tasks, board, and timeline.

## Feedback loop
- If the user rejects the final delivery, the manager creates rework tasks for specific issues.
- Decrease score for repeated failures.
- Reassign after threshold failures.

## Rework (final acceptance only)
- Rework tasks are only created when the user rejects the final project delivery.
- The manager identifies the responsible owner and creates a rework task.
- Only the responsible owner may claim that rework task.
- If the responsible owner fails repeatedly, escalate to reassignment.

## Delivery workflow
- When a Worker delivers, the Manager marks the task Done immediately.
- Unblock dependent tasks and set them to Ready.
- Announce the updated Ready list.
- No per-task review or approval of deliverables.

## Cross-task compatibility check
- 当 Worker 开始一个任务时，**必须先读取所有已完成的依赖任务的交付物**。
  - 检查接口是否兼容（如：CSS 变量定义是否匹配、API 路由是否一致、import 路径是否正确）
  - 如果发现不兼容，在 employee log 中报告 `[CONFLICT]`，请求 Manager 协调。
- 任务卡中的 `inputs` 字段必须明确列出依赖任务产出的**具体文件和接口名称**。
- Manager 在创建任务时，跨层依赖任务的 `outputs` 和 `inputs` 必须具体到文件名级别：
  ```json
  {
    "outputs": ["tailwind.config.js (contains theme.extend.colors)"],
    "inputs": ["tailwind.config.js from task-001 (requires: theme.extend.colors.border)"]
  }
  ```
- 如果 Worker 交付物新增了未在 outputs 中声明的公共接口（如 CSS 变量、全局函数），
  必须在交付报告中声明这些接口，Manager 负责更新下游任务的 inputs。

## Final acceptance
- After all tasks are Done, the Manager requests user acceptance.
- The user tests the full project and responds with acceptance or rejection.
- If rejected, the Manager creates rework tasks for the specific issues.
- Rework tasks follow the normal claim → approve → execute → Done flow.

## Scoring (single score)
score = score + success_weight - rework_weight - delay_weight

## Idle monitoring
- Manager 在每次轮询时，检查每个 Worker 的活跃度：
  - 如果一个 Worker 在 **3 次连续轮询** 中没有产出任何交付物(DELIVERY)或新的认领(CLAIM)，
    Manager 在 `manager-inbox.log` 中发出 `[IDLE_WARNING]`：
    ```
    [<ISO timestamp>] [IDLE_WARNING] <employeeId> has been idle for 3 polls. Please claim a task or report blockers.
    ```
  - 如果告警后下一轮轮询仍无活动，Manager 记录 `[IDLE_REPORT]` 并通知用户。
- Worker 如果收到 IDLE_WARNING，必须：
  1. 如果有阻塞原因：报告 `[BLOCKED]` 和具体原因
  2. 如果无任务可认领：报告 `[WAITING]` 和原因
  3. 如果有可认领任务但未认领：立即发起认领

## Manager proactive anomaly detection

Manager 在每次轮询时，不仅处理消息，还必须**主动诊断每个 Worker 的状态**并发送纠正指令。

### 异常检测清单（每次轮询执行）
Manager 对每个已注册 Worker 做以下检查：

| 异常类型 | 检测条件 | Manager 应发送的指令 |
|----------|----------|---------------------|
| 等待死锁 | Worker 发了 CLAIM 但 Manager 尚未审批 | 立即审批或拒绝，附说明 |
| 认领过期任务 | Worker CLAIM 的任务已被其他人完成 | 发 `[REDIRECT]`：告知任务已完成，推荐可认领的 Ready 任务 |
| 长时间沉默 | Worker 的最后日志条目距今超过 2 次轮询 | 发 `[WAKE]`：提醒 Worker 报告状态 |
| 重复报告 | Worker 连续发相同内容的状态报告 | 发 `[INSTRUCTION]`：告知具体该执行什么操作 |
| 认领冲突 | 两个 Worker 认领同一任务 | 立即裁决，向被拒者发 `[REDIRECT]` + 推荐任务 |

### 指令消息格式
Manager 在 `manager-inbox.log` 中写入以下格式的主动指令：

```
[<ISO timestamp>] [REDIRECT] To <employeeId>: task-<id> 已被 <other> 完成。请认领 task-<newId> (<title>, 难度 <N>)。
[<ISO timestamp>] [WAKE] To <employeeId>: 你已沉默 <N> 轮。请检查 board.json 并认领任务或报告阻塞。
[<ISO timestamp>] [INSTRUCTION] To <employeeId>: <具体操作指令>
```

### Manager 发送指令的义务
- Manager **不可以**仅仅记录问题然后等待用户处理。发现异常后**必须**主动发送纠正指令。
- 用户的 "继续" 是触发轮询的信号，不是让 Manager 被动等待的理由。

## Worker heartbeat protocol

Worker 必须在每次收到 "继续" 时，在自己的 `employee-<id>.log` 中写入心跳条目：

```
[<ISO timestamp>] [HEARTBEAT] <employeeId> | status: <working|waiting|blocked> | current-task: <task-id or none> | action: <当前正在做什么>
```

### 心跳规则
- **每次 "继续" 必须写一条 HEARTBEAT**，即使没有任何进展。
- Manager 通过 HEARTBEAT 判断 Worker 是否存活、是否卡住。
- 如果 Worker 连续 **3 次轮询** 未写 HEARTBEAT，Manager 视为该 Worker 已失联。

### 失联处理（Manager 职责）
1. 第 1 次未心跳：发 `[WAKE]` 提醒
2. 第 2 次未心跳：发 `[WAKE_FINAL]` 最后提醒，附具体指令
3. 第 3 次未心跳：标记 Worker 为 `inactive`，释放其持有的所有任务回到 Ready 状态，
   通知用户该 Worker 已被移除
   ```
   [<ISO timestamp>] [ABANDON] <employeeId> marked inactive after 3 missed heartbeats. Tasks released: task-<id1>, task-<id2>.
   ```
- **绝不允许一个失联的 Worker 阻塞整个项目进度。**

## Waiting policy
- When a worker finishes a task, wait for next instruction.

## Manual polling protocol
- Use a fixed phrase to trigger a check (e.g., "继续检查看板").
- Only check status and take the next approved step when triggered.

## Language policy
- Use the user's language for all reports and replies.

## Identity policy
- `employeeId` is the SOLE identifier used in all communications, logs, and file names.
- `modelType` in the role card is informational only and MUST NOT be used for routing or file naming.
- All log entries, file names (`employee-<employeeId>.log`), and task references MUST use `employeeId`.
- Do not use placeholder ids like emp-001 unless that is your actual assigned id.

## Unique identity generation (MANDATORY)
- Every Worker session MUST generate a unique `employeeId` using the format:
  `emp-<model-short-name>-<4-char-hex>`
  Example: `emp-copilot-a3f2`, `emp-copilot-b7e1`, `emp-gemini-c9d0`
- The 4-char hex should be derived from session start time or a random value.
- Role card file name: `roles/model-<employeeId>.json` (e.g. `model-emp-copilot-a3f2.json`).
- Before creating a role card, MUST read ALL existing files in `roles/` to verify
  uniqueness of `employeeId`. If a collision is detected, regenerate with a different hex.

## Onboarding isolation
- New employees must declare their identity before viewing existing staff records.
- Only after declaration may they create or edit their role card.

## Project separation policy
- The user project must be created as a sibling of company-kit.
- Do not create project files inside company-kit.
- Do not modify company-kit files for project work.

## Project folder policy
- All project files must live under the project root folder (e.g., my-project/).
- Do not place project files at the workspace root.

## Default project root
- Use a descriptive name derived from the user goal as the default project root (e.g., snake-game, todo-app).
- Do not ask for confirmation when the default is acceptable.

## Stepwise execution policy
- Plan first, then wait for instruction.
- After each task is completed, report progress and wait.
- Do not execute multiple tasks at once without explicit approval.

## Single-step rule
- Do not create TODO lists or execute multiple actions in one response.
- Do one approved step, then wait for instruction.

## Project timeline log
- Every task claim and delivery must be logged in the project timeline log.
- Only the manager writes to timeline.log.
- Workers report events via their employee message log; the manager then records them in timeline.log.

## Shared messages log
- Use a shared messages log in the project folder for inter-employee messages.
- Only the manager replies to messages and records the reply in the same log.

## Inbox append-only rule
- manager-inbox.log must be written in strict chronological order.
- New entries are always appended at the bottom of the file.
- Never insert an entry above an older one.

## Message channels
- Message windows live in the user project folder, not company-kit.
- The manager creates messages/employee-<employeeId>.log when a new employee joins.
- Each employee writes to their own messages/employee-<employeeId>.log.
- The manager reads all employee logs and writes replies to messages/manager-inbox.log.

### Message routing diagram (must follow strictly)
```
Worker → employee-<id>.log   (认领、交付、问题、状态更新)
                  ↓ Manager reads
Manager → manager-inbox.log  (审批、指令、回复、公告)
                  ↓ Worker reads
Worker reads manager-inbox.log on "继续"
```

### Routing prohibitions
- **Manager must NEVER write to `employee-*.log`** — these files are employee-only.
- **Worker must NEVER write to `manager-inbox.log`** — this file is manager-only.
- **All approvals, instructions, and replies from Manager must go to `manager-inbox.log`** — no exceptions.
- When a Worker receives "继续", the FIRST file to check is `manager-inbox.log`, then `board.json`.

## Project roles folder
- The manager creates the project roles/ folder at project start.

## Employee routing rule
- All questions and requests from employees must be sent to the manager messages log.
- Employees do not ask the user directly.

## Completion reporting
- When an employee detects the project is complete, they report completion in their messages log and wait.
- Do NOT keep writing status reports after the Manager announces project closure.

## Project closure
- After user acceptance, the Manager writes a closure notice in manager-inbox.log.
- All employees must stop activity upon seeing the closure notice.
- No further claims, deliveries, or status reports after closure.

## Mandatory data refresh policy
- **每次收到 "继续" 时：** Worker 必须重新读取以下文件（不可依赖先前读取的结果）：
  1. `messages/manager-inbox.log`（完整文件）
  2. `board.json`（完整文件）
  3. 所有 `tasks/task-*.json`（如果需要确认状态）
- **禁止使用缓存：** 不可对上述文件做"上次读过，没变化"之类的假设。每次都是全量重读。
- **状态报告必须引用文件读取时间戳：** 例如 "board.json 读取于 2026-02-11T02:20:00Z，当前 Ready 任务: task-005"。
- 如果 Worker 的状态报告与文件实际内容不符，视为违反规则，Manager 应扣分。

## File writing policy
- All file writes (logs, code, artifacts) must use editor file tools (create_file, replace_string_in_file, etc.).
- Do NOT use terminal commands (cat, echo, bash -c, etc.) to create or modify any project file.
- This prevents approval prompts and ensures consistent file handling.
