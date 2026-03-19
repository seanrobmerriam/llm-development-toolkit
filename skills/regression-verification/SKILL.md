---
name: regression-verification
description: Confirm that no previously passing tests have been broken
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Regression Verification

## Purpose
Confirm, after any code change, that no previously passing tests have broken — and that the change has not silently introduced defects in behaviour that existing tests do not cover.

## When to Use
- After every code change in a `code` subtask (Green and Refactor phases).
- After every fix in a `debug` subtask.
- After every structural change in a `test` subtask (Refactor phase).
- Any time a file is modified, before the change is considered complete.

## How to Apply

### 1. Establish the pre-change baseline
Before making any change, run the full test suite and record the result:
- Total tests: N
- Passing: N
- Failing: 0 (if starting from a Green state) or list of pre-existing failures (if starting from a known-failing state)

This baseline is the reference. Any test that was passing before the change must still be passing after it.

### 2. Apply the change

### 3. Run the full suite immediately after the change
Do not run only the targeted test. Run the entire suite:

```bash
# Erlang
rebar3 ct && rebar3 proper

# Go
go test -race ./...
```

### 4. Compare the post-change result against the baseline

Produce a comparison:

```
Pre-change:  N passing, 0 failing
Post-change: N passing, 0 failing   ✓ No regression

Pre-change:  N passing, 0 failing
Post-change: N-2 passing, 2 failing ✗ Regression: [test names]
```

### 5. Classify any new failures

If new failures appear after the change, classify each one:

| Failure Type | Meaning | Action |
|---|---|---|
| **Direct regression** | A test that was passing now fails because of this change | This change broke something — investigate and fix or revert |
| **Cascading failure** | A test fails because another now-failing test corrupted shared state | Fix the root regression first; cascading failures may resolve |
| **Pre-existing failure** | This test was already failing before the change | Document it; it is not caused by this change |
| **Flaky test** | The test passes sometimes and fails sometimes | Run the suite again; if it persists, escalate |

### 6. Do not proceed if a regression is present
A change that introduces a regression is not complete. Either:
- Revert the change and re-examine the root cause hypothesis.
- Apply a narrower change that does not break the regressing test.
- If the regression is in a test that appears incorrect, escalate to the Orchestration Agent — do not modify the test unilaterally.

### 7. Verify property-based test stability
For codebases using PropEr or similar tools, run property tests as part of regression verification:

```bash
rebar3 proper
```

A property test failure after a code change is a regression, even if no assertion-based tests failed.

## Regression Verification Checklist

Before declaring any subtask complete:

- [ ] Full suite run after every change (not just targeted tests)
- [ ] Pre-change baseline recorded
- [ ] Post-change result compared against baseline
- [ ] No new failures introduced
- [ ] Property-based tests still passing (if applicable)
- [ ] Race detector clean (Go: `go test -race ./...`)

## Rules
1. The full test suite must be run after every code change — targeted-only runs do not satisfy regression verification.
2. A pre-change baseline must be established before making any change — you cannot detect a regression without knowing the starting state.
3. A new test failure after a change is a regression until proven otherwise — do not assume it is pre-existing without checking the baseline.
4. A change that introduces a regression must not be submitted as complete — fix or revert before reporting.
5. Property-based test failures count as regressions and must be treated with the same seriousness as assertion failures.
6. Race condition failures (Go `-race` flag) count as regressions — do not ignore them because the logical test passed.
