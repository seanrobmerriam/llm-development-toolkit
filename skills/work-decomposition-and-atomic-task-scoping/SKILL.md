---
name: work-decomposition-and-atomic-task-scoping
description: Break every subphase from the development plan into concrete, atomic tasks and subtasks
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Work Decomposition and Atomic Task Scoping

## Purpose
Break every subphase from the development plan into concrete, atomic tasks and subtasks — each small enough to be completed by a single agent in a single lifecycle — without losing coverage of the subphase's exit criteria.

## When to Use
- Producing `task-plan.md` from `development-plan.md`.
- Validating that a proposed set of subtasks fully satisfies a subphase's exit criteria.
- Splitting an oversized task that spans multiple concerns.

## How to Apply

### 1. Start from exit criteria, not from the architecture
For each subphase, read its exit criteria first. Every exit criterion must be satisfiable by at least one subtask. Work backwards from "what must be true when this is done" to "what work produces that state".

### 2. Define tasks at the concern level
A task is a unit of work that addresses a single coherent concern within a subphase (e.g. "Implement the Account aggregate", "Write property-based tests for balance invariants", "Integrate the Cowboy HTTP router"). A task is too large if it requires two different types of subagent to execute it.

### 3. Define subtasks at the action level
A subtask is a single atomic action that one subagent can complete in one lifecycle. A well-scoped subtask:
- Targets a specific named file, module, function, handler, or test suite.
- Has a single clear output (a working function, a passing test, a fixed bug).
- Can be described in one sentence that includes a verb, a target, and an outcome.

Examples of well-scoped subtasks:
- `Implement the CreateAccount Cowboy handler in src/handlers/account_handler.erl`
- `Write unit tests for the balance_invariant/2 property in test/ledger_prop_tests.erl`
- `Fix the OPTIONS handler missing CORS headers in src/router.erl`

Examples of poorly-scoped subtasks:
- `Build the account service` (too large, covers multiple concerns)
- `Fix the bugs` (no specific target)
- `Implement and test the ledger` (two types of work, two agents required)

### 4. Assign a type annotation to every subtask
Every subtask must be annotated with exactly one type:
- `code` — writing or modifying implementation code
- `test` — writing or modifying test code
- `debug` — diagnosing and fixing a known error or failing test

### 5. Check coverage before finalising
After decomposing all subtasks for a subphase, verify:
- Every exit criterion of the subphase is addressed by at least one subtask.
- No exit criterion is addressed by zero subtasks (coverage gap).
- No subtask addresses something not required by any exit criterion (scope creep).

## Rules
1. Every subtask must be executable by a single agent in a single lifecycle — subtasks that require two agents or two passes are too large.
2. Every subtask must name the specific file, module, function, or component it acts on — generic descriptions are not acceptable.
3. No subtask may appear more than once in the plan — duplication is always a planning error.
4. Every subtask must have exactly one type annotation (`code`, `test`, or `debug`).
5. The full set of subtasks for a subphase must satisfy all of that subphase's exit criteria — incomplete coverage is a defect in the task plan, not in the downstream agents.
