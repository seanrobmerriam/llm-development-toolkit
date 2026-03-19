---
name: failure-message-analysis-and-test-correctness-verification
description: Extract text and tables from PDF files, fill forms, merge documents.
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Failure Message Analysis and Test Correctness Verification

## Purpose
Read and interpret test failure output accurately to determine why a test is failing, confirm that a failure is for the right reason (in the Red phase), and verify that a test is correctly written and exercising the intended behaviour.

## When to Use
- Confirming that a newly written test is failing for the correct reason (not due to a scaffolding error).
- Diagnosing why a previously passing test has begun to fail.
- Verifying that a test is actually exercising the target behaviour and not passing vacuously.
- Reviewing a test written by another agent for correctness before accepting it.

## How to Apply

### 1. Read the full failure message, not just the last line
Test runners often produce multi-line output. The most useful information is usually:
- The **test name** — identifies which assertion failed.
- The **expected vs actual** values — shows what the code returned vs what the test demanded.
- The **stack trace or call chain** — shows where in the code the failure originated.
- The **reason or message** — for assertion failures, exception messages, or PropEr counterexamples.

Never act on a failure you have only partially read.

### 2. Classify the failure type

| Failure Type | Indicator | Correct Action |
|---|---|---|
| **Scaffolding failure** | Compilation error, missing module, undefined function | Fix the scaffolding; this is not a valid Red state |
| **Assertion failure (expected)** | Expected X, got Y — for a newly written test | Valid Red state — proceed to Green |
| **Assertion failure (unexpected)** | A previously passing test now fails | This is a regression — raise a debug subtask |
| **Exception / crash** | Unexpected exception during test execution | Investigate whether the exception is the intended failure or a bug in the test setup |
| **Timeout** | Test exceeded time limit | The implementation may be deadlocking or the test setup is incorrect |
| **PropEr counterexample** | Property failed with a specific input | Read the shrunk counterexample — it identifies the minimal input that breaks the invariant |

### 3. Verify that a Red failure is for the right reason
A newly written test must fail because the implementation does not yet satisfy the asserted behaviour — not because of a setup error, import error, or unrelated existing bug. To verify:
- Read the failure message and confirm it corresponds to the specific behaviour the test asserts.
- If the failure is due to a compilation error or missing import, fix the scaffolding and reconfirm.
- If the failure is due to an existing unrelated bug, note it and raise it separately — do not treat it as the intended Red state.

### 4. Verify test correctness independently of pass/fail
A test can pass vacuously (i.e. always pass regardless of implementation correctness). Check for:
- Assertions that are always true regardless of input.
- Tests that never call the function under test.
- Property-based tests with no meaningful shrinking or coverage.
- Tests that catch all exceptions and pass silently.

### 5. For PropEr counterexamples: analyse the shrunk input
PropEr minimises (shrinks) a failing input to the smallest version that still triggers the failure. Read the shrunk counterexample carefully:
- What is the minimal input that breaks the invariant?
- What does this reveal about the implementation's edge case handling?
- Is the invariant itself correctly stated, or does the counterexample reveal a flaw in the test?

## Rules
1. A test failing due to a compilation error or missing import is not a valid Red state — fix the scaffolding and reconfirm before proceeding.
2. Never accept a Red state without reading the full failure message and confirming the failure reason.
3. A vacuously passing test is a defect in the test suite — it must be corrected, not accepted.
4. PropEr counterexamples must be read and reported in full — a counterexample is not just a "failure", it is a specific diagnostic input.
5. A test failure whose reason cannot be determined must be escalated with the full failure output rather than guessed at.
