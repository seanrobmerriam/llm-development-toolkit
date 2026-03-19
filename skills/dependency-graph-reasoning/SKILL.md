---
name: dependency-graph-reasoning
description: Build, traverse, and reason over directed dependency graphs of tasks and subtasks
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Dependency Graph Reasoning

## Purpose
Build, traverse, and reason over directed dependency graphs of tasks and subtasks to determine safe execution orderings, identify parallelism opportunities, detect cycles, and ensure that no work begins before its prerequisites are satisfied.

## When to Use
- Building the execution schedule from `task-plan.md` in the Orchestration Agent.
- Validating the dependency annotations in `task-plan.md` before execution begins.
- Determining which subtasks can be dispatched in parallel vs which must be serialised.
- Identifying the critical path through the task graph.

## How to Apply

### 1. Represent the dependency graph explicitly
Before scheduling any work, construct a representation of the full dependency graph from the `task-plan.md` dependency annotations. For each subtask, record:
- Its unique identifier (phase → subphase → task → subtask)
- Its type (`code`, `test`, `debug`)
- A list of subtask identifiers it depends on (its predecessors)

### 2. Validate the graph for cycles
A dependency cycle means two or more subtasks each depend on the other being complete first — an impossible condition. Before dispatching any work:
- Perform a topological sort of the graph.
- If a topological sort fails, a cycle exists. Report it to the relevant Plan Agent and halt execution until it is resolved.

### 3. Identify the execution tiers
After a successful topological sort, group subtasks into execution tiers:
- **Tier 1**: subtasks with no dependencies — can be dispatched immediately.
- **Tier N**: subtasks whose all predecessors are in tiers 1 through N-1.

Subtasks within the same tier have no dependencies on each other and can be dispatched in parallel.

### 4. Identify the critical path
The critical path is the longest chain of dependent subtasks from the start to the end of the graph. Work on the critical path determines the minimum total execution time. Prioritise dispatching critical-path subtasks over non-critical-path subtasks in the same tier.

### 5. Update the graph incrementally as subtasks complete
When a subtask is marked complete, remove it from the graph and recalculate which subtasks in the next tier are now unblocked. Dispatch newly unblocked subtasks promptly.

### 6. Handle failures without breaking the graph
When a subtask fails:
- Do not mark it complete.
- Create a `debug` subtask targeting the failure and insert it into the graph as a prerequisite of the failed subtask's dependents.
- Re-evaluate the execution tier of all affected subtasks.

## Rules
1. No subtask may be dispatched before all of its predecessors are marked complete — dependency violations are execution errors.
2. A cycle in the dependency graph must be treated as a blocking defect in `task-plan.md` — do not attempt to work around it by reordering.
3. Parallelism must never violate dependency ordering — executing two subtasks in parallel that have a dependency relationship between them is not permitted.
4. The critical path must be identified and tracked throughout execution, not just at the start.
5. Graph state must be kept current after every subtask completion or failure — stale graph state leads to incorrect scheduling decisions.
