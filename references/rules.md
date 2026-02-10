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
- Prefer parallelizable splits.
- Keep ownership simple and clear.
- Make dependencies explicit.
- Every decomposed task must be created as its own task card.
- **Requirement coverage check**: After decomposition, the Manager must verify that every requirement from the user goal is covered by at least one task. If any requirement is missing, add a task for it before marking any task Ready.

## Assignment principles
- Tasks are claimed by workers, not pre-assigned in the plan.
- Match difficulty to model level and score.
- Allow high-score models to take harder tasks.
- One task, one owner.
- Do not claim tasks with unmet dependencies.
- A worker may hold only one active task at a time.

## Approval gate
- Claim a task, then wait for approval before starting work.
- No approval means no work is allowed.

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

## Final acceptance
- After all tasks are Done, the Manager requests user acceptance.
- The user tests the full project and responds with acceptance or rejection.
- If rejected, the Manager creates rework tasks for the specific issues.
- Rework tasks follow the normal claim → approve → execute → Done flow.

## Scoring (single score)
score = score + success_weight - rework_weight - delay_weight

## Waiting policy
- When a worker finishes a task, wait for next instruction.

## Manual polling protocol
- Use a fixed phrase to trigger a check (e.g., "继续检查看板").
- Only check status and take the next approved step when triggered.

## Language policy
- Use the user's language for all reports and replies.

## Identity policy
- Use your real model id from the project roles/model-<id>.json in all logs and reports.
- Do not use placeholder ids like model-001 unless that is your actual id.
- Model name/id must match the actual model. Do not guess or substitute.
- If multiple employees use the same model, assign a unique employee instance id.

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

## Status reporting policy
- Before any status report, re-check the latest task files and timeline log.
- Do not reuse prior status summaries without re-checking.

## File writing policy
- All file writes (logs, code, artifacts) must use editor file tools (create_file, replace_string_in_file, etc.).
- Do NOT use terminal commands (cat, echo, bash -c, etc.) to create or modify any project file.
- This prevents approval prompts and ensures consistent file handling.
