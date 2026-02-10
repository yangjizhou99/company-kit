# Task Contract (Field Guide)

## Core fields
- id: unique task id
- title: short name
- description: problem statement
- difficulty: 1-5
- dependencies: task ids required before start (only real data dependencies)
- concurrencyLayer: integer (0, 1, 2...) indicating which parallel wave this task belongs to. Layer 0 = no dependencies. Layer N = depends on at least one task in Layer N-1. Tasks in the same layer can execute concurrently.
- parallelizable: true/false
- assignee: employeeId (the unique employee identifier, NOT model id)
- restrictedAssignee: only this owner may claim
- status: task state
- approval: approval status block
- inputs: required inputs — **must specify exact file names and interface names from dependency tasks** (e.g., "tailwind.config.js with theme.extend.colors.border defined")
- outputs: expected outputs — **must specify exact file names and any public interfaces they expose** (e.g., "src/index.css exposing CSS variables: --border, --background, --foreground")
- acceptanceCriteria: pass/fail checks
- logs: timeline of events
- blame: responsibility record (owner + reason)
- reworkOf: original task id (for rework tasks)

## Status machine
New -> Decomposing -> Ready -> In Progress (Approved) -> Done

## No per-task review
- There is no Review or Rework status during normal flow.
- When Worker delivers, Manager marks Done and unblocks dependents.
- Review/Rework only happens if the user rejects the final project delivery.

## Ready rules
- A task can be Ready only when all dependencies are Done.
- Ready tasks are eligible to be claimed by workers.

## Ownership rules
- One task, one owner.
- Owner is responsible for quality and completeness.
- If restrictedAssignee is set, only that owner may claim.

## Approval rules
- A task may be claimed when Ready.
- Work starts only after explicit approval.

## Approval fields
- required: whether approval is required (default true)
- approved: true when approval is granted
- approvedBy: reviewer or manager id
- approvedAt: ISO timestamp

## Logs format
- at: ISO timestamp
- by: employeeId or manager id
- event: Created | Claimed | Delivered | Done | Blocked | Rework
- details: short summary

## Acceptance rules
- Criteria must be explicit and testable.
- Acceptance is done by the user at the end of the project, not per-task.

## Rework rules
- Rework tasks are only created when the user rejects the final delivery.
- Manager identifies the responsible owner and creates a rework task.
- Rework tasks should set reworkOf and restrictedAssignee.
