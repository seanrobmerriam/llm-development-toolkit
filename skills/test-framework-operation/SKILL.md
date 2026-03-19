---
name: test-framework-operation
description: Correctly configure, invoke, and interpret the output of the test frameworks
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Test Framework Operation

## Purpose
Correctly configure, invoke, and interpret the output of the test frameworks used in the project — including unit test runners, integration test suites, and property-based testing tools — to produce reliable, reproducible test results that agents and the Orchestration Agent can act on.

## When to Use
- Running the test suite to confirm a Red state (failing test exists).
- Running the test suite to confirm a Green state (all tests passing).
- Running a targeted subset of tests to isolate a specific failure.
- Interpreting test output to distinguish root-cause failures from cascading failures.

## Stack-Specific Guidance

### Erlang / OTP (Rebar3, Common Test, PropEr)

**Run the full suite:**
```bash
rebar3 ct
```

**Run a specific test suite:**
```bash
rebar3 ct --suite=test/account_SUITE
```

**Run property-based tests (PropEr via rebar3):**
```bash
rebar3 proper
```

**Run a specific property:**
```bash
rebar3 proper --module=ledger_prop_tests --prop=prop_balance_invariant
```

**Interpret Common Test output:**
- `ok` — all cases in the suite passed.
- `FAILED` — one or more cases failed. Read the HTML report at `_build/test/logs/` for detail.
- `{skip, Reason}` — a case was skipped; investigate whether the skip is intentional.

**Interpret PropEr output:**
- `OK: Passed N tests` — property holds for all generated inputs.
- `Failed! After N tests` — a counterexample was found. Read the shrunk counterexample carefully.

---

### Go (standard `testing` package, `go test`)

**Run the full suite:**
```bash
go test ./...
```

**Run a specific package:**
```bash
go test ./internal/account/...
```

**Run a specific test:**
```bash
go test -run TestCreateAccount ./internal/account/...
```

**Run with race detector:**
```bash
go test -race ./...
```

**Interpret output:**
- `ok` — package passed.
- `FAIL` — one or more tests failed. Read the output for the specific test name and failure message.
- `--- FAIL: TestName` — identifies the failing test case.

---

## General Principles

### Confirm the failure reason, not just the failure
A test that fails because of a missing import, compilation error, or test setup problem is not a valid Red state. Fix the scaffolding issue first, then confirm the test fails for the intended behavioural reason.

### Run the full suite before and after every change
Always establish the baseline suite state before making changes, and run the full suite after. A change that fixes one test but breaks another is not a net improvement.

### Distinguish root-cause failures from cascading failures
When multiple tests fail, the first failure in execution order is often the root cause. Fix the earliest failure first and re-run before addressing subsequent failures — many may resolve automatically.

## Rules
1. Test suite invocation must use the correct command for the project's stack — do not guess or use a generic command.
2. A compilation failure is not a test failure — fix compilation before interpreting test results.
3. The full suite must be run after any code change — targeted test runs are supplementary, not a substitute for the full suite.
4. PropEr counterexamples must be read and understood, not dismissed — a shrunk counterexample is diagnostic gold.
5. Test results must be reported with the exact failure message, not paraphrased — accuracy in failure reporting is essential for effective debugging.
