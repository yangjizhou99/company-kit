# Task Contract (Field Guide)

## Core fields
- id: unique task id
- title: short name
- description: problem statement
- difficulty: 1-5
- dependencies: task ids required before start
- parallelizable: true/false
- assignee: model id
- restrictedAssignee: only this owner may claim
- status: task state
- approval: approval status block
- inputs: required inputs
- outputs: expected outputs
- acceptanceCriteria: pass/fail checks
- logs: timeline of events
- blame: responsibility record (owner + reason)
- reworkOf: original task id (for rework tasks)

## Status machine
New -> Decomposing -> Ready -> In Progress (Approved) -> Review -> Done
Review -> Rework -> Review

## Review task flow
- When a task enters Review, create a review task card.
- Review tasks are claimed like any other task.
- Completing the review task updates the main task to Done or Rework.

## Ready rules
- A task can be Ready only when all dependencies are Done.
- Ready tasks are eligible to be claimed by workers.

## Ownership rules
- One task, one owner.
- Owner is responsible for quality and completeness.
- Review tasks have their own owner (reviewer).
- If restrictedAssignee is set, only that owner may claim.

## Approval rules
- A task may be claimed when Ready.
- Work starts only after explicit approval.
- Review work starts only after separate approval.

## Approval fields
- required: whether approval is required (default true)
- approved: true when approval is granted
- approvedBy: reviewer or manager id
- approvedAt: ISO timestamp

## Logs format
- at: ISO timestamp
- by: model id or manager id
- event: Created | Claimed | Delivered | Reviewed | Rework | Done | Blocked
- details: short summary

## Acceptance rules
- Criteria must be explicit and testable.
- If a failure is found, record cause and owner.

## Rework rules
- If output fails, manager marks Rework and assigns next action.
- After N failures, reassign to a different model.
- Rework tasks should set reworkOf and restrictedAssignee.
