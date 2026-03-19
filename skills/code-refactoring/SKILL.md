---
name: code-refactoring
description: Improve the internal structure, clarity, and maintainability of existing code
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Code Refactoring

## Purpose
Improve the internal structure, clarity, and maintainability of existing code — whether implementation or test code — without changing its observable behaviour, and verify that the improvement has not introduced regressions.

## When to Use
- The Refactor phase of the TDD cycle, after the suite is Green.
- Improving test code for clarity and reduced duplication after the suite is Green.
- Removing dead code, simplifying overly complex logic, or aligning code with the architecture plan.

## When NOT to Use
- Before the suite is Green — refactoring on a failing suite is not permitted.
- To add new behaviour or fix a bug — those are `code` and `debug` subtasks respectively.
- To change the observable behaviour of the code under test — if a refactor changes what the code does, it is not a refactor.

## How to Apply

### 1. Establish a Green baseline before starting
Run the full test suite and confirm it is passing before making any refactoring change. A refactor must start from a known-good state.

### 2. Refactor in small, independently verifiable steps
Do not make all refactoring changes at once. Apply one change at a time and run the suite after each step. This makes it trivial to identify which change caused a regression.

### 3. Apply refactoring patterns deliberately

**For implementation code:**
- **Extract function**: move a coherent block of logic into a named function with a clear purpose.
- **Rename**: improve the clarity of a variable, function, or module name to better reflect its domain meaning.
- **Remove duplication**: replace repeated logic with a single shared function.
- **Simplify conditionals**: replace nested if/case expressions with pattern matching, guard clauses, or helper functions.
- **Align with architecture**: ensure module and function boundaries match the component boundaries defined in `architecture-plan.md`.

**For test code:**
- **Extract test helper**: move repeated setup or assertion logic into a shared helper function.
- **Remove test duplication**: if two tests assert the same behaviour, remove the weaker one.
- **Improve test names**: rename tests to clearly describe the behaviour being verified, not the implementation detail.
- **Clarify assertion messages**: ensure failure messages identify the expected and actual values meaningfully.

### 4. Do not change test assertions during test refactoring
Refactoring a test means improving its structure, not its assertions. Changing what a test asserts is not a refactor — it is a change to the test's specification and must be reviewed before being accepted.

### 5. Run the full suite after every step and after completion
The suite must be Green after every individual refactoring step and after all refactoring is complete. A refactor that leaves the suite in a failing state is not complete.

## Rules
1. Do not begin refactoring until the full test suite is Green — refactoring on a failing suite is not permitted.
2. Run the suite after each individual change — do not batch all changes and run once at the end.
3. A refactor must not change the observable behaviour of the code — if a test starts failing after a refactor, the refactor changed behaviour and must be reverted or reconsidered.
4. Do not add new features during a refactor phase — that belongs in a new `code` subtask.
5. Do not fix bugs during a refactor phase — that belongs in a `debug` subtask.
6. Test assertions must not be changed during test refactoring — only structure and readability improvements are permitted.
