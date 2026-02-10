# Test 002 Plan (Complex, Risk-Finding)

Goal: Reveal weak points in decomposition, ownership, acceptance, and handoff.

## Task summary
Build a small, verifiable mini feature with multiple subtasks and clear I/O.

## What this test should expose
- Ambiguous requirements and missing inputs
- Dependency management and handoff quality
- Responsibility when integration fails
- Score adjustment fairness

## Steps
1) Manager decomposes into 3-5 tasks with dependencies.
2) Worker claims tasks in sequence (single worker to stress handoff and context).
3) Worker delivers per-task outputs with references to acceptance criteria.
4) Manager verifies each task and the integrated output.
5) Manager records feedback and updates score if needed.

## Acceptance criteria
- Each subtask has explicit inputs/outputs.
- Integration output matches reference examples.
- Errors can be traced to a specific subtask.

## Suggested task
Implement a small text processor with three functions:
1) normalize_text(s): lowercases and trims extra spaces.
2) word_frequencies(s): returns a dict of word -> count.
3) top_k_words(s, k): returns a list of top k words sorted by count desc, then alphabet asc.

Reference examples:
- normalize_text("  Hello   World ") -> "hello world"
- word_frequencies("a a b") -> {"a": 2, "b": 1}
- top_k_words("b a a c c c", 2) -> ["c", "a"]

Why complex:
- Multiple functions, shared normalization rules.
- Sorting requirements and tie-breaking.
- Integration path and error attribution.
