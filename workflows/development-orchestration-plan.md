# Development Orchestration Plan

1. Read `task-plan.md` in full, including all phases, subphases, tasks, subtasks, type annotations, and dependency annotations.
2. Read `architecture-plan.md` to maintain architectural context when composing instructions for subagents.
3. Build a dependency-resolved execution order from the subtask graph, identifying which subtasks can be executed in parallel and which must be serialised.
4. For each subtask, compose a self-contained instruction set for the appropriate subagent type — the instruction must include: the subtask objective, all relevant context from the architecture and task plan, the exact files and components to act on, and the acceptance criteria the subagent must satisfy before considering its work complete.
5. Dispatch each subtask to a new subagent of the correct type: **Code Agent** for `code` subtasks, **Test Agent** for `test` subtasks, **Debug Agent** for `debug` subtasks.
6. After each subagent completes, verify its output against the subtask's acceptance criteria before marking it done and unblocking dependent subtasks.
7. If a subagent's output fails acceptance criteria, raise a new `debug` subtask and dispatch it to a new Debug Agent — do not re-use the failed agent.
8. Continue dispatching until every subtask in `task-plan.md` is complete and all subphase exit criteria from `development-plan.md` are satisfied.

## Skills to use
1. Dependency graph traversal and execution scheduling
2. Subagent instruction composition
3. Output verification against acceptance criteria
4. Escalation and debug-task generation

## Rules to follow
1. No subagent completes more than one subtask in its lifecycle — a new subagent is spawned for every subtask, without exception.
2. Each subagent instruction must be fully self-contained — the subagent must not need to read the task plan itself to understand what to do.
3. Code Agents write working implementation code only; they do not write tests.
4. Test Agents write tests only; they do not write implementation code.
5. Debug Agents fix errors and bugs only; they do not add new features or write new tests from scratch.
6. The Orchestration Agent must never implement, test, or debug code directly — its only role is coordination and verification.
7. A subphase is not complete until all its subtasks have passed their acceptance criteria — partial completion is not acceptable.
8. Parallelism is encouraged but must never violate dependency ordering from the task plan.
