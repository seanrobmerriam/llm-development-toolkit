---
name: root-cause-analysis-and-stack-trace-interpretation
description: Diagnose the true origin of a defect or failure 
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Root Cause Analysis and Stack Trace Interpretation

## Purpose
Diagnose the true origin of a defect or failure by systematically reading failure output, tracing execution paths, and distinguishing root causes from downstream symptoms — so that fixes address the actual problem rather than masking it.

## When to Use
- A Debug Agent receiving a failing test or error report from the Orchestration Agent.
- Any situation where a test is failing for an unexpected reason.
- Distinguishing a root-cause failure from cascading failures triggered by it.
- Deciding where in the codebase a fix should be applied.

## Core Distinction: Root Cause vs Symptom

A **symptom** is the observable failure: a test assertion that fails, an exception that is thrown, a value that is wrong.

A **root cause** is the underlying defect that produces the symptom: a missing guard clause, an incorrect data transformation, an uninitialised variable, a race condition.

Fixing a symptom without identifying the root cause produces a fragile patch. The fix may pass the current test while leaving the underlying defect in place to manifest elsewhere.

## How to Apply

### 1. Reproduce the failure before analysing it
Run the failing test exactly as specified in the instruction. Confirm you observe the same failure. Do not analyse a failure you have not reproduced — the failure output may have changed since the report was written.

### 2. Read the full failure output from top to bottom

**For an assertion failure:**
- What was expected?
- What was actually returned?
- Which specific assertion triggered the failure?
- What was the call chain that produced the actual value?

**For an exception / crash:**
- What is the exception type and message?
- Where in the call stack did it originate? (The deepest frame in application code is usually closest to the root cause.)
- What inputs were being processed when the crash occurred?

**For a PropEr counterexample:**
- What is the shrunk input that triggers the failure?
- Which invariant was violated?
- What does the minimal failing input reveal about the edge case?

### 3. Trace backwards from the failure site to the root cause

Use the following sequence:
1. Identify the **failure site** — the specific line of code where the incorrect behaviour manifests.
2. Identify the **proximate cause** — what immediate condition at the failure site produced the wrong outcome (e.g. a nil value where a non-nil was expected).
3. Trace **where that condition came from** — follow the data backwards through the call chain.
4. Repeat until you reach a point where the incorrect state was first introduced — this is the root cause.

### 4. Document the root cause hypothesis before fixing
Write a short hypothesis in the following form before changing any code:

```
Root cause hypothesis:
  The [function/module] at [file:line] [does/returns/fails to] X
  because [condition Y] is [true/false/unhandled] when [input Z] is provided.
  This propagates to [failure site] where the test assertion fails.
```

If you cannot complete this hypothesis, you do not yet understand the root cause. Continue tracing.

### 5. Validate the hypothesis
Before applying the fix, confirm the hypothesis:
- Can you predict what change will make the test pass?
- Does the hypothesis explain all observed failures, or only some?
- Would the fix introduce a new failure elsewhere?

## Stack-Specific Notes

### Erlang / OTP
- OTP crash reports include the process name, the failing MFA (module:function/arity), and the reason. Read all three.
- `{badmatch, Value}` errors mean a pattern match failed — identify which pattern and what value was received.
- `{undef, [{Module, Function, Arity, _}]}` means the function does not exist or is not exported — check the `-export` attribute.
- `{noproc, ...}` in gen_server calls means the process is not running — check the supervision tree.

### Go
- Goroutine stack traces list frames from most recent to oldest. The root cause is usually in the lower frames.
- `nil pointer dereference` at a specific line: trace back to where the pointer was set (or not set).
- Race conditions appear under `go test -race` — the report identifies both conflicting goroutines and the lines involved.

## Rules
1. Always reproduce the failure before forming any hypothesis — analysis of a failure you have not observed is guesswork.
2. Document the root cause hypothesis explicitly before applying any fix — a fix without a documented hypothesis cannot be reviewed or validated.
3. The root cause must be in application code — "the test is wrong" is only a valid root cause after confirming the test behaviour with the Orchestration Agent.
4. A hypothesis that only explains some of the observed failures is incomplete — all observed failures must be accounted for.
5. Do not apply a fix that masks the symptom without addressing the root cause — a passing test achieved by swallowing an error is not a fix.
