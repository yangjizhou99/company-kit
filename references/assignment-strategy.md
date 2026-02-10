# Assignment Strategy

This defines how to match tasks to models based on difficulty and score.
Workers claim tasks themselves after checking eligibility.

## Inputs
- task difficulty (1-5)
- model level (1-5)
- model score (0-200)

## Rules
- Only claim if model level >= task difficulty.
- Prefer higher score for harder tasks.
- If multiple candidates, the highest score should claim first.

## Fallback
- If no model meets level, escalate to manager.
- After N failures, re-open the task for another model to claim.
