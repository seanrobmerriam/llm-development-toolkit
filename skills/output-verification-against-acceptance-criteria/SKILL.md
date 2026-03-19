---
name: output-verification-against-acceptance-criteria
description: Systematically evaluate a subagent's delivered output
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Output Verification Against Acceptance Criteria

## Purpose
Systematically evaluate a subagent's delivered output against the acceptance criteria specified in its instruction set, and produce a clear, binary pass/fail determination for each criterion before marking a subtask as complete.

## When to Use
- The Orchestration Agent reviewing a Code, Test, or Debug Agent's completed output.
- Any stage in the pipeline where a document or deliverable must be validated before the next stage begins.

## How to Apply

### 1. Retrieve the original instruction and its acceptance criteria
Before reviewing any output, re-read the instruction that was sent to the subagent. Do not evaluate output against criteria from memory — read the criteria fresh to avoid drift.

### 2. Evaluate each criterion independently
Go through the acceptance criteria one by one. For each criterion:
- State the criterion explicitly.
- Determine whether the output satisfies it: **Pass** or **Fail**.
- If Fail, state specifically what is missing or incorrect.

Do not bundle criteria. A criterion that partially passes is a fail.

### 3. Run the test suite as part of verification
For all `code` and `debug` subtasks, verification is not complete until the test suite has been run and its output reviewed:
- All tests must pass.
- No previously passing test may have been broken by the subagent's changes.
- A test suite that was already failing before the subtask began must not be treated as a baseline — the pre-subtask suite state must be known before dispatching.

### 4. Verify file and component targets
Confirm that the subagent acted on all target files listed in the instruction, and only those files. Changes to unlisted files should be flagged and reviewed before acceptance.

### 5. Produce a verification summary
After evaluating all criteria, produce a structured summary:

```markdown
## Verification: [Subtask ID] — [Subtask Title]

**Overall result:** [PASS | FAIL]

| Criterion | Result | Notes |
|---|---|---|
| [Criterion 1 text] | PASS | — |
| [Criterion 2 text] | FAIL | Missing error handling for empty account ID |

**Test suite result:** [All passing | N failures — list them]
**Files modified:** [List files changed by the subagent]
**Unlisted file changes:** [None | List any unexpected modifications]

**Action:** [Mark complete | Raise debug subtask for: ...]
```

### 6. Raise a debug subtask for every failure
If any criterion fails, do not mark the subtask complete. Instead, raise a new `debug` subtask that:
- References the failing criterion(s) by number.
- Includes the relevant test output or error details.
- Targets the specific file(s) where the defect was identified.

## Rules
1. A subtask is only marked complete when every acceptance criterion is met — partial pass is a fail.
2. Test suite execution is mandatory for all `code` and `debug` subtask verifications — no exceptions.
3. Each acceptance criterion must be evaluated independently — a bundle result of "mostly passes" is not acceptable.
4. Unlisted file modifications must be reviewed before acceptance — unreviewed side-effects are a risk to the codebase.
5. The Orchestration Agent must never mark a subtask complete on trust — every completion must be verified against the criteria.
