# Company Kit (Drop-in Manager)

This folder is a drop-in manager kit for any project. It is guidance-first (no automatic file edits).

## How to use
1) Read SKILL.md.
2) Read references/rules.md, references/onboarding.md, references/task-contract.md.
3) Add or edit a model role card in the project roles/.
4) Create the user project as a sibling of company-kit.
5) Create tasks using task-template.json.
6) Manually update task status and logs as work progresses.
7) Keep company-kit clean; do not modify it during project work.

## Minimal flow
- Manager model decomposes a user goal into tasks.
- Worker models claim tasks by matching difficulty and role.
- Manager validates outputs and records feedback.
- If failed, the manager adjusts scores and reassigns.

## What to customize
- Update model-role.json for each model you add.
- Copy task-template.json to create real tasks.
- Extend the rules if your team has extra policies.
