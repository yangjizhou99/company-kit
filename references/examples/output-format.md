# Standard Output Format

Use this format when delivering work:

## Deliverable
- Summary: one sentence
- Output: code or artifact
- Mapping: show how outputs meet acceptance criteria
- Notes: edge cases or assumptions

## Example
Summary: Implemented sum_to_n(n) using formula.

Output (Python):
```python
def sum_to_n(n: int) -> int:
    return n * (n + 1) // 2
```

Mapping:
- sum_to_n(1) == 1
- sum_to_n(3) == 6
- sum_to_n(10) == 55

Notes:
- Assumes n is a positive integer.
