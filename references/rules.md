# Rules

## Roles
- Manager: decomposes work, assigns tasks, validates outputs, resolves failures.
- Worker: executes tasks and reports results.
- Reviewer (including user): claims review tasks, performs acceptance, and completes review.
 - Manager must not claim or execute worker tasks.
- Manager must not switch roles or impersonate a worker.
- Manager must not ask the user to act as a worker.

## Task lifecycle
New -> Decomposing -> Ready -> In Progress -> Review -> Done
Review -> Rework -> Review

## Decomposition principles
- Prefer parallelizable splits.
- Keep ownership simple and clear.
- Make dependencies explicit.
- Every decomposed task must be created as its own task card.

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
- Review tasks also require a separate claim and approval.

## Command authority
- Only the manager's explicit approval authorizes work.
- Generic prompts like "继续" only trigger a status check and waiting behavior.

## Ready visibility policy
- Tasks eligible to start must be marked Ready.
- When tasks are marked Ready, announce the Ready list.
- New members should only claim from Ready.

## Board control
- Only the manager may modify the task board or task status files.
- Workers must request changes via the messages log.

## Feedback loop
- If output fails, identify cause and owner.
- Decrease score for repeated failures.
- Reassign after threshold failures.

## Responsibility and rework
- When a bug is confirmed, create a rework task bound to the responsible owner.
- Only the responsible owner may claim that rework task.
- If the responsible owner fails repeatedly, escalate to reassignment.

## Acceptance workflow
- Review only tasks in Review status.
- If pass, set status to Done and announce Ready tasks that are unblocked.
- If fail, set status to Rework and record the reason.
## Post-review hard rules
- After any review decision, update the main task status.
- Unblock dependent tasks and set them to Ready immediately.
- Announce the updated Ready list.

## Review task policy
- Review is a separate claimable task.
- Review tasks can be claimed by reviewers, including the user.
- Reviewer must announce the claim and follow the acceptance checklist.

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
- All project files must live under the project root folder (e.g., snake-game/).
- Do not place project files at the workspace root.

## Default project root
- Use snake-game as the default project root unless the user specifies another name.
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

## Shared messages log
- Use a shared messages log in the project folder for inter-employee messages.
- Only the manager replies to messages and records the reply in the same log.

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

## Status reporting policy
- Before any status report, re-check the latest task files and timeline log.
- Do not reuse prior status summaries without re-checking.
