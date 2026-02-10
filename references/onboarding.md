# Onboarding (Model Join Guide)

## What to read (in order)
1) references/rules.md
2) project roles/
3) task-template.json
4) tests/ (optional examples)

## Join checklist
- Read rules and acknowledge the policies.
- Read the project roles/ and confirm role, level, and score.
- Confirm your model id and use it in all logs.
- Create or update your own role card in project roles/ with your model id.
- Confirm your exact model name and use it in your role card.
- If your model is shared, create a unique employee instance id.
- Declare your identity before viewing any existing staff records.
- Only after declaring identity may you create or edit your role card.
- Ask the manager if any missing inputs block your work.
- Claim one task at a time that matches your difficulty range.
- Provide output in the requested format and wait for next instruction.
- Create or choose a project folder as a sibling of company-kit.
- Do not place user project files inside company-kit.
- Use the user's language for all reports and replies.
- Do not modify company-kit files for project work.
- Never claim a task with unmet dependencies.
- Re-check task files and the timeline log before reporting status.
- Send all questions to the manager via messages/employee-<employeeId>.log.
- When you receive "continue", check manager-inbox.log and then act or wait.

## If roles/ is missing
- Notify the manager via messages log to create the project roles/ folder.

## How to claim a task
- Write a claim announcement in your messages/employee-<employeeId>.log.
- Include: task ID, title, your model id, start time.
- **Use file editing tools (create_file / replace_string_in_file) to write logs. Do NOT use terminal commands (cat, echo, etc.) to write to any file.**
- Do NOT edit task files, board.json, or timeline.log — those are manager-only.
- Wait for approval in manager-inbox.log before doing any work.

## How to deliver
- Create or edit the deliverable files (code, artifacts) using file tools, not terminal commands.
- Write a delivery report in your messages/employee-<employeeId>.log:
  - List of created/modified files.
  - Map outputs to acceptance criteria.
  - Delivery time.
- **Never use terminal commands (cat >>, echo, bash) to write or append to files. Always use the editor's file tools.**
- Do NOT change task status or board — the manager will update them.
- After delivery, wait for the next instruction.

## If blocked
- Add a log entry with the blocker.
- Ask the manager for clarification.

## Stepwise execution
- Produce a plan first and wait for approval.
- Execute tasks in order, one step at a time.
- Never complete multiple tasks in a single response.
- Do not create TODO lists; execute only one approved step, then wait.
- Treat "continue" as a polling signal, not approval.
