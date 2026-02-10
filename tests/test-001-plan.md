# Test 001 Plan (End-to-End)

Goal: Validate full flow with 1 manager + 1 worker using a code task that is easy to verify.

## Task summary
- Implement a small function with deterministic outputs.
- Verify with fixed input/output pairs.

## Steps
1) Manager creates a task from task-template.json.
2) Worker claims the task and produces the code + brief explanation.
3) Manager verifies outputs against acceptance criteria.
4) Manager records feedback in test-001-feedback.json.
5) If failed, manager triggers rework and updates score.

## Acceptance criteria
- The function returns correct results for listed inputs.
- Code is minimal and readable.
- Outputs match exactly.

## Suggested task
Implement a function `sum_to_n(n)` that returns 1+2+...+n.
- sum_to_n(1) == 1
- sum_to_n(3) == 6
- sum_to_n(10) == 55
