# Rules

## Roles
- Manager: decomposes work, assigns tasks, validates outputs, resolves failures.
- Worker: executes tasks and reports results.
- Reviewer (including user): claims review tasks, performs acceptance, and completes review.

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

## Ready visibility policy
- Tasks eligible to start must be marked Ready.
- When tasks are marked Ready, announce the Ready list.
- New members should only claim from Ready.

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

## Language policy
- Use the user's language for all reports and replies.

## Identity policy
- Use your real model id from roles/model-<id>.json in all logs and reports.
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

## Stepwise execution policy
- Plan first, then wait for instruction.
- After each task is completed, report progress and wait.
- Do not execute multiple tasks at once without explicit approval.

## Single-step rule
- Do not create TODO lists or execute multiple actions in one response.
- Do one approved step, then wait for instruction.

## Project timeline log
- Every task claim and delivery must be logged in the project timeline log.

## Status reporting policy
- Before any status report, re-check the latest task files and timeline log.
- Do not reuse prior status summaries without re-checking.
