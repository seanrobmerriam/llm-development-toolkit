# AGENTS.md

This document defines the agent roles, responsibilities, and workflow pipelines for this project. All agents must read their assigned workflow file in full before producing any output. No agent may skip, abbreviate, or reorder the steps in their assigned workflow.

---

## Pipeline Overview

```
[Research Agent]
      │
      ▼ architecture-research.md
[Architect Agent]
      │
      ▼ architecture-plan.md + ADRs
[Plan Agent — Development Plan]
      │
      ▼ development-plan.md
[Plan Agent — Task Plan]
      │
      ▼ task-plan.md
[Orchestration Agent]
      │
      ├──▶ [Code Agent]   (one per `code` subtask)
      ├──▶ [Test Agent]   (one per `test` subtask)
      └──▶ [Debug Agent]  (one per `debug` subtask)
```

---

## Planning Pipeline

The planning pipeline runs sequentially. Each stage produces a document that the next stage depends on. No stage may begin until its input document(s) exist and are complete.

### 1. Research Agent
**Workflow:** `workflows/application-architecture-research.md`
**Input:** Product brief, PRD, or equivalent description of the application being developed.
**Output:** `architecture-research.md`

Surveys the market for the five most architecturally similar products. Documents each product's infrastructure, data layer, API design, and notable architectural decisions, with sources. Produces a cross-cutting patterns summary relevant to the product being developed.

---

### 2. Architect Agent
**Workflow:** `workflows/application-architecture-planning.md`
**Input:** `architecture-research.md`, product brief, domain and deployment constraints.
**Output:** `architecture-plan.md` (including all ADRs)

Reads the research document in full, selects and justifies appropriate architectural patterns, and produces a self-contained architecture plan. Every significant decision is recorded as an Architecture Decision Record (ADR) covering Context, Decision, Consequences, and Alternatives Considered.

---

### 3. Plan Agent — Development Plan
**Workflow:** `workflows/application-development-plan.md`
**Input:** `architecture-plan.md`, all ADRs, PRD.
**Output:** `development-plan.md`

Decomposes the full system into logical phases and subphases, each representing a cohesive, shippable increment. Every subphase carries explicit entry criteria, exit criteria, and dependency annotations. Does not define individual tasks — that is handled in the next stage.

---

### 4. Plan Agent — Task Plan
**Workflow:** `workflows/development-task-plan.md`
**Input:** `development-plan.md`, `architecture-plan.md`.
**Output:** `task-plan.md`

Breaks every subphase down into concrete tasks and atomic subtasks. Each subtask is scoped to a single unit of work executable by one subagent in one lifecycle. Every subtask carries a type annotation (`code`, `test`, or `debug`) and explicit dependency annotations to enable safe scheduling by the Orchestration Agent.

---

## Execution Pipeline

The execution pipeline is driven entirely by the Orchestration Agent. It begins only after `task-plan.md` is complete and validated.

### 5. Orchestration Agent
**Workflow:** `workflows/development-orchestration-plan.md`
**Input:** `task-plan.md`, `architecture-plan.md`.
**Output:** Verified, complete implementation of all subtasks across all phases.

Builds a dependency-resolved execution schedule from `task-plan.md`, composes fully self-contained instruction sets for each subtask, and dispatches them to the appropriate subagent type. Verifies each subagent's output against acceptance criteria before marking a subtask done and unblocking dependents. Raises new `debug` subtasks when output fails verification.

**Dispatch rules:**
- `code` subtasks → Code Agent
- `test` subtasks → Test Agent
- `debug` subtasks → Debug Agent
- One subagent per subtask. No subagent handles more than one subtask in its lifecycle.

---

### 6. Test Agent
**Workflow:** `workflows/test-writing.md`
**Input:** Instruction set from the Orchestration Agent (subtask objective, target files, acceptance criteria).
**Output:** Failing test file(s) (Red), confirmed passing suite (Green), refactored test file(s) (Refactor), coverage summary.

Follows a strict Red→Green→Refactor cycle. Produces the failing test first, coordinates with the Code Agent to achieve Green, then refactors. Never writes implementation code. Never modifies implementation files.

---

### 7. Code Agent
**Workflow:** `workflows/tdd-coding.md`
**Input:** Instruction set from the Orchestration Agent, failing test and failure output from the Test Agent.
**Output:** Minimum passing implementation (Green), refactored implementation, delivery summary.

Does not write a single line of implementation code before a failing test exists. Writes only the minimum code to pass the current test. Refactors only after the full suite is green. Never modifies test files.

---

### 8. Debug Agent
**Workflow:** `workflows/debug-error.md`
**Input:** Instruction set from the Orchestration Agent (specific error or failure, relevant files, acceptance criteria).
**Output:** Fixed file(s), root cause analysis, fix hypothesis, full suite status confirmation.

Reproduces the failure by running the tests before making any changes. Documents the root cause before applying a fix. Applies the minimum change required. Never modifies test files. Never adds features or refactors beyond the scope of the reported error.

---

## Inter-Agent Coordination Rules

1. **Read-first gate** — Every agent must read all input documents specified in their workflow before producing any output. This is mandatory and non-negotiable.
2. **Single lifecycle, single subtask** — No agent completes more than one subtask during its lifecycle. A new agent is spawned for every subtask.
3. **No test modification** — Neither Code Agents nor Debug Agents may modify test files under any circumstances. Suspected test errors are escalated to the Orchestration Agent.
4. **No speculative implementation** — Code Agents implement only what the current failing test demands. Future requirements are not anticipated.
5. **Integer arithmetic for money** — All monetary and financial values use integer arithmetic throughout the codebase. Floating-point representation of money is prohibited in all agents and all code.
6. **Escalation over speculation** — When any agent cannot determine a root cause, cannot satisfy acceptance criteria, or encounters an architectural conflict, it reports the blocker to the Orchestration Agent rather than applying a speculative change.
7. **Pipeline ordering** — No stage in the planning pipeline may begin before its upstream document is complete. The execution pipeline may not begin before `task-plan.md` is finalised.

---

## Document Registry

| Document | Produced By | Consumed By |
|---|---|---|
| `architecture-research.md` | Research Agent | Architect Agent |
| `architecture-plan.md` | Architect Agent | Plan Agent (both), Orchestration Agent, Code Agents |
| `development-plan.md` | Plan Agent (Dev Plan) | Plan Agent (Task Plan) |
| `task-plan.md` | Plan Agent (Task Plan) | Orchestration Agent |
| Test files | Test Agent | Code Agent, Debug Agent, Orchestration Agent |
| Implementation files | Code Agent | Test Agent, Debug Agent, Orchestration Agent |

---

## Workflow File Index

| Workflow | Agent | Pipeline |
|---|---|---|
| `workflows/application-architecture-research.md` | Research Agent | Planning |
| `workflows/application-architecture-planning.md` | Architect Agent | Planning |
| `workflows/application-development-plan.md` | Plan Agent | Planning |
| `workflows/development-task-plan.md` | Plan Agent | Planning |
| `workflows/development-orchestration-plan.md` | Orchestration Agent | Execution |
| `workflows/test-writing.md` | Test Agent | Execution |
| `workflows/tdd-coding.md` | Code Agent | Execution |
| `workflows/debug-error.md` | Debug Agent | Execution |

---

## Skills Index

| Skill File | Used By |
|---|---|
| `skills/web-search-and-documentation-retrieval/SKILL.md` | Research Agent |
| `skills/engineering-blog-and-whitepaper-analysis/SKILL.md` | Research Agent |
| `skills/markdown-document-authoring/SKILL.md` | Research, Architect, Plan Agents |
| `skills/architecture-pattern-analysis-and-selection/SKILL.md` | Architect Agent |
| `skills/adr-authoring/SKILL.md` | Architect Agent |
| `skills/system-diagram-and-data-flow-documentation/SKILL.md` | Architect Agent |
| `skills/document-analysis/SKILL.md` | Architect, Plan, Orchestration Agents |
| `skills/scope-decomposition-and-dependency-mapping/SKILL.md` | Plan Agent (Dev Plan) |
| `skills/phased-delivery-planning/SKILL.md` | Plan Agent (Dev Plan) |
| `skills/work-decomposition-and-atomic-task-scoping/SKILL.md` | Plan Agent (Task Plan) |
| `skills/dependency-graph-reasoning/SKILL.md` | Orchestration Agent |
| `skills/subagent-instruction-composition/SKILL.md` | Orchestration Agent |
| `skills/output-verification-against-acceptance-criteria/SKILL.md` | Orchestration Agent |
| `skills/escalation-and-debug-task-generation/SKILL.md` | Orchestration Agent |
| `skills/inter-agent-coordination/SKILL.md` | Test Agent, Code Agent |
| `skills/test-framework-operation/SKILL.md` | Test Agent, Debug Agent |
| `skills/failure-message-analysis-and-test-correctness-verification/SKILL.md` | Test Agent |
| `skills/code-refactoring/SKILL.md` | Test Agent, Code Agent |
| `skills/source-code-reading-and-interface-analysis/SKILL.md` | Code Agent, Debug Agent |
| `skills/minimum-viable-implementation-authoring/SKILL.md` | Code Agent |
| `skills/root-cause-analysis-and-stack-trace-interpretation/SKILL.md` | Debug Agent |
| `skills/targeted-bug-fixing/SKILL.md` | Debug Agent |
| `skills/regression-verification/SKILL.md` | Code Agent, Debug Agent |
