---
name: targeted-bug-fixing
description: Apply the minimum code change required to resolve a confirmed root cause
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Targeted Bug Fixing

## Purpose
Apply the minimum code change required to resolve a confirmed root cause, without introducing new failures, altering unrelated behaviour, or expanding the scope of the fix beyond what the Debug Agent was instructed to address.

## When to Use
- After a root cause hypothesis has been formed and documented (see `root-cause-analysis-and-stack-trace-interpretation`).
- A `debug` subtask where the failure has been reproduced and understood.

## When NOT to Use
- Before a root cause hypothesis is documented — fixes without root cause analysis are patches, not repairs.
- To refactor code beyond the scope of the fix.
- To add new features or handle new cases not related to the reported failure.

## Core Principle: Minimum Effective Change

The smallest change that resolves the root cause is the correct fix. A larger change that also resolves it is a worse fix, because every additional line is an additional surface for new failures and review overhead.

## How to Apply

### 1. Confirm the root cause hypothesis is documented
Before touching any file, verify that the root cause hypothesis (from `root-cause-analysis-and-stack-trace-interpretation`) is written down. If it is not, produce it first.

### 2. Identify the exact location(s) of the fix
The root cause hypothesis points to a specific file and line (or a small set of them). The fix belongs there. Do not make changes elsewhere unless the hypothesis explicitly requires it.

### 3. Design the fix before applying it
Write out what the fix will be in plain language before editing any file:
```
Fix: In [file:line], change [what] to [what] because [root cause hypothesis].
Expected effect: The failing test will pass because [explanation].
No other tests should be affected because [explanation].
```

### 4. Apply the fix in the smallest possible change
- Change only the lines identified by the root cause hypothesis.
- Do not reformat surrounding code.
- Do not rename variables or refactor logic unless the renaming is the fix.
- Do not add comments unless they clarify the fix itself.

### 5. Run the targeted test first, then the full suite

**Step 1** — Run only the failing test:
```bash
# Erlang
rebar3 ct --suite=test/failing_suite --case=failing_case

# Go
go test -run TestFailingCase ./path/to/package/...
```
Confirm it now passes.

**Step 2** — Run the full suite:
```bash
# Erlang
rebar3 ct && rebar3 proper

# Go
go test -race ./...
```
Confirm no previously passing tests have broken.

### 6. If the fix causes new failures, treat them as new debug subtasks
Do not attempt to chain-fix multiple independent failures in a single subtask. If the fix resolves the reported failure but introduces a new one:
- Report the new failure to the Orchestration Agent.
- The Orchestration Agent will raise a new `debug` subtask for it.
- Do not attempt to fix the new failure within this subtask's scope.

## Stack-Specific Fix Patterns

### Erlang / OTP

| Root Cause | Fix Pattern |
|---|---|
| Missing pattern match clause | Add the missing clause with correct return value |
| Missing CORS / OPTIONS handler | Add the `options` clause to the Cowboy route handler |
| Unreleased `js.FuncOf` closure (Go/Wasm) | Add `defer fn.Release()` immediately after `js.FuncOf` |
| Incorrect Mnesia transaction return | Wrap in `mnesia:transaction/1` and match `{atomic, Result}` |
| `{badmatch, Value}` on function call result | Add error tuple handling: `{ok, V} -> ...; {error, R} -> ...` |

### Go

| Root Cause | Fix Pattern |
|---|---|
| nil pointer dereference | Add a nil guard before the dereference |
| Integer overflow in monetary arithmetic | Use `int64` throughout; add overflow check if required |
| Goroutine leak | Ensure all goroutines have an exit path via `context.Cancel` or channel close |
| Missing error return check | Capture and handle the error return value |
| Incorrect JSON array serialisation | Use a slice type rather than a map for array encoding |

## Rules
1. Document the root cause hypothesis before applying the fix — no exceptions.
2. Change only the lines the root cause hypothesis identifies — do not expand scope during the fix.
3. Run the targeted failing test first, then the full suite — both must pass before the fix is considered complete.
4. If the fix introduces a new failure, report it to the Orchestration Agent rather than fixing it within this subtask.
5. Never modify test files to make a failing test pass — if the test is believed to be incorrect, escalate to the Orchestration Agent.
6. All monetary values must remain in integer arithmetic after the fix — do not introduce floats to resolve an arithmetic issue.
