---
name: minimum-viable-implementation-authoring
description: For creating the smallest, simplest implementation that makes a failing test pass
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---
# SKILL: Minimum Viable Implementation Authoring

## Purpose
Write the smallest, simplest implementation that makes a failing test pass — no more, no less — so that the codebase grows in provably correct, test-backed increments and speculative code never enters the codebase ahead of its test coverage.

## When to Use
- The Green phase of the TDD cycle, after receiving a failing test from the Test Agent.
- Any `code` subtask where the entry condition is a failing test.

## When NOT to Use
- Before a failing test exists — implementation without a test is not permitted.
- During the Refactor phase — the Refactor phase improves existing Green code, it does not extend it.
- To implement future requirements that are not yet covered by a test.

## Core Principle: YAGNI + Red → Green

The only valid question when writing Green code is: **"What is the minimum code that makes this specific test pass?"**

Not: "What will we probably need later?"
Not: "What would be cleaner if I also added X?"
Not: "The architecture suggests I should also handle Y."

Write exactly what the test demands. Nothing more.

## How to Apply

### 1. Read the failing test completely before writing a line
Understand precisely what the test is asserting:
- What function is being called?
- With what inputs?
- What output or side effect is expected?
- What error conditions are being tested?

### 2. Write the simplest code that satisfies the assertion
Start with the absolute minimum — even if it seems too simple:
- If the test asserts a return value, return that value.
- If the test asserts a side effect, produce that side effect.
- If the test asserts an error condition, handle that error condition.

Do not add error handling, logging, validation, or optimisation that is not demanded by the current test.

### 3. Run the suite — confirm Green, confirm no regressions
After writing the implementation:
1. Run the full test suite.
2. Confirm the new test passes.
3. Confirm no previously passing tests have broken.

If either condition is not met, the implementation is not complete.

### 4. Resist the urge to over-implement
Common over-implementation traps:
- **Handling inputs the test never exercises**: if the test does not pass a nil, do not handle nil yet.
- **Adding logging or metrics**: these are separate subtasks.
- **Generalising prematurely**: if the test only covers one case, implement one case.
- **Implementing the "obvious next step"**: the next test will drive the next step.

### 5. Respect the architecture constraints from the instruction
The instruction from the Orchestration Agent will include relevant architectural constraints. These are non-negotiable even in the minimum implementation:
- **Integer arithmetic for money**: never use floats for monetary values, even in the simplest case.
- **Module boundaries**: do not reach across module boundaries that the architecture defines as separate.
- **Error tuple conventions**: use the established `{ok, Value}` / `{error, Reason}` or equivalent pattern from the start.

## Stack-Specific Notes

### Erlang / OTP
- Start with the minimum exported function. Do not implement the full `gen_server` callback set if the test only exercises `handle_call`.
- Use pattern matching as the primary dispatch mechanism — it is both minimal and idiomatic.
- Return `{ok, State}` or `{error, Reason}` tuples consistently from the first implementation.

### Go
- Return the minimum from a function — do not add return values the test does not inspect.
- Use the simplest struct that satisfies the interface under test.
- Return concrete errors (`errors.New(...)`) before introducing custom error types unless the test requires them.

## Rules
1. Do not write implementation code before a failing test exists — no exceptions.
2. Write only the code required to pass the current failing test — do not implement untested functionality.
3. The full test suite must be Green after the implementation — a new passing test that breaks an existing test is not a valid Green state.
4. All monetary values must use integer arithmetic from the first implementation — this is never retrofitted later.
5. Never modify test files to achieve Green — if the test appears incorrect, escalate to the Orchestration Agent.
