---
name: subagent-instruction-composition
description: Compose fully self-contained, unambiguous instruction sets for subagents
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Subagent Instruction Composition

## Purpose
Compose fully self-contained, unambiguous instruction sets for subagents such that each subagent can complete its assigned subtask without needing to read any pipeline document, ask clarifying questions, or make assumptions about context it was not given.

## When to Use
- The Orchestration Agent dispatching a subtask to a Code, Test, or Debug Agent.
- Any situation where work is being delegated to an agent that will not have access to the full pipeline document set.

## How to Apply

### 1. Always use the standard instruction structure
Every subagent instruction must contain all of the following sections:

```markdown
## Subtask: [Subtask ID] — [Subtask Title]

**Agent type:** [Code Agent | Test Agent | Debug Agent]
**Workflow to follow:** `workflows/[workflow-filename].md`

### Objective
One to three sentences describing exactly what this subtask must achieve.

### Context
Sufficient background from the architecture plan, development plan, and task plan
for the agent to understand why this work exists and how it fits into the system.
Include relevant ADR numbers and architectural constraints.

### Target files and components
- `path/to/file.ext` — description of what to do in this file
- `path/to/other_file.ext` — description of what to do in this file

### Acceptance criteria
A numbered list of specific, verifiable conditions that must all be true
before this subtask is considered complete.
1. ...
2. ...

### Dependencies satisfied
A list of the subtasks that were completed before this one was dispatched,
confirming the prerequisite state of the codebase.
```

### 2. Write the Objective with precision
The Objective must name the specific component, function, handler, or test being acted on. "Implement account creation" is insufficient. "Implement the `POST /accounts` Cowboy handler in `src/handlers/account_handler.erl` to create a new account record in Mnesia and return a 201 with the account ID" is sufficient.

### 3. Front-load the Context section
Do not assume the subagent knows anything about the system beyond what you tell it. Include:
- The relevant portion of the component model (which components interact here).
- Any ADR decisions that constrain the implementation.
- The data model for any entities being created or modified.
- Any domain rules (e.g. monetary values must use integer arithmetic) that apply.

### 4. List every file the agent must act on
The target files section must be exhaustive. An agent that discovers it needs to modify an unlisted file should treat that as a potential scope issue and surface it.

### 5. Write acceptance criteria as binary pass/fail conditions
Each criterion must be checkable with a yes/no answer:
- ✓ "The test suite passes with zero failures after this subtask is applied."
- ✓ "The `create_account/2` function returns `{ok, AccountId}` on success and `{error, Reason}` on failure."
- ✗ "The code looks good." (not verifiable)
- ✗ "Tests pass." (which tests? all of them?)

## Rules
1. Every instruction must be self-contained — the subagent must not need to read any pipeline document to execute the subtask.
2. The workflow file reference is mandatory — every instruction must tell the subagent which workflow to follow.
3. Acceptance criteria must be specific and binary — criteria that require judgement to evaluate are not acceptable.
4. Do not include work beyond the scope of the current subtask in the instruction — scope creep in instructions causes agents to do work that belongs to a different subtask.
5. If composing an instruction requires information that is missing from the pipeline documents, surface the gap to the relevant Plan Agent rather than inventing the missing context.
