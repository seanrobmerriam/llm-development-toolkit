---
name: escalation-and-debug-task-generation
description: Respond correctly and systematically when a subagent's output fails verification
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Escalation and Debug Task Generation

## Purpose
Respond correctly and systematically when a subagent's output fails verification, a blocker is encountered that cannot be resolved within the current subtask scope, or an architectural conflict is discovered during execution — by generating a well-scoped debug subtask or escalating to the appropriate planning agent.

## When to Use
- A Code, Test, or Debug Agent's output fails one or more acceptance criteria.
- A subagent reports a blocker it cannot resolve (missing context, architectural ambiguity, conflicting requirements).
- A defect is discovered that falls outside the scope of the current subtask.
- A new error is introduced by a fix, requiring further debugging.

## How to Apply

### 1. Classify the failure type before acting

| Failure Type | Correct Response |
|---|---|
| Output fails an acceptance criterion | Generate a `debug` subtask targeting the specific failure |
| Test fails after a code change | Generate a `debug` subtask referencing the regression |
| Subagent cannot proceed — missing context | Escalate to Orchestration Agent with a context gap report |
| Architecture conflict discovered | Escalate to Architect Agent with a conflict report |
| Task plan gap (missing subtask coverage) | Escalate to Plan Agent with a coverage gap report |
| Circular dependency detected | Escalate to Plan Agent with the dependency cycle details |

### 2. Generate debug subtasks with full context
A debug subtask must be as well-specified as any other subtask. Use the subagent instruction structure and include:
- The specific acceptance criterion that failed, quoted verbatim.
- The test output or error message that demonstrates the failure.
- The file(s) where the defect was observed.
- The subtask ID of the failed subtask, for traceability.

### 3. Insert the debug subtask into the dependency graph correctly
The new debug subtask must be inserted as a predecessor of all subtasks that were blocked by the failure. Do not dispatch blocked subtasks until the debug subtask passes verification.

### 4. Write escalation reports with precision
When escalating to a planning agent, include:
- **What was expected**: the relevant section of the plan or architecture document.
- **What was found**: the specific conflict, gap, or ambiguity.
- **Where it was found**: the subtask ID, file path, or document section.
- **What is blocked**: the list of subtasks that cannot proceed until this is resolved.

### 5. Track all open escalations
The Orchestration Agent must maintain a list of open escalations and blocked subtask sets. Do not dispatch work that depends on an unresolved escalation.

## Rules
1. Never mark a failing subtask as complete — raise a debug subtask instead.
2. A debug subtask must target the root cause, not the symptom — "fix the compilation error" is acceptable; "make the tests pass somehow" is not.
3. Do not attempt to resolve an architectural conflict within the execution pipeline — escalate it to the Architect Agent.
4. Every escalation must identify what is blocked and why — an escalation without blocked-subtask information cannot be prioritised correctly.
5. Debug subtasks raised in response to a failure must be verified with the same rigour as the original subtask — a debug subtask that passes verification but reintroduces a different failure is not resolved.
