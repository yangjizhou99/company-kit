# Decomposition and Scheduling Rules

This defines how to split work and maximize parallel execution.

## Decomposition steps
1) Identify deliverables and acceptance criteria.
2) Split into smallest independent tasks.
3) Mark dependencies explicitly.
4) Assign difficulty 1-5 to each task.

## Parallelization rules
- Prefer tasks with no shared state.
- Avoid parallel tasks that require the same artifact at the same time.
- If two tasks can start after the same dependency, mark both parallelizable.

## Scheduling heuristics
- Start all tasks with no dependencies first.
- Prioritize tasks on the critical path.
- If a task unblocks multiple others, schedule it earlier.

## Ownership clarity
- One task, one owner.
- If unclear ownership, split further.
