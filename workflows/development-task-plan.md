# Development Task Plan

1. Read `development-plan.md` in full, including all phases, subphases, entry/exit criteria, and dependency annotations.
2. Read `architecture-plan.md` to ensure tasks align precisely with the intended architecture.
3. For each subphase in the development plan, enumerate every concrete task required to satisfy that subphase's exit criteria.
4. For each task, enumerate its subtasks — each subtask must be scoped to a single, atomic unit of work executable by one subagent in one lifecycle (e.g. "Implement the `CreateAccount` handler", "Write property-based tests for the ledger balance invariant").
5. Annotate every subtask with its type: `code`, `test`, or `debug`.
6. Annotate inter-task dependencies explicitly so the Orchestration Agent can determine safe execution order and parallelism opportunities.
7. Produce the final task plan as `task-plan.md`, structured as a hierarchy: Phase → Subphase → Task → Subtask, with type and dependency annotations at the subtask level.

## Skills to use
1. Development plan and architecture document analysis
2. Work decomposition and atomic task scoping
3. Dependency graph reasoning
4. Markdown document authoring

## Rules to follow
1. Read both `development-plan.md` and `architecture-plan.md` in full before producing any output.
2. Every subtask must be executable by a single subagent in a single lifecycle — subtasks that span multiple concerns must be split.
3. Subtask descriptions must be unambiguous: include the specific file, module, function, or component being acted on wherever possible.
4. Every subtask must have a type annotation of exactly one of: `code`, `test`, or `debug`.
5. No subtask may appear more than once in the plan — duplication is a planning error.
6. The task plan must achieve full coverage of the development plan: every exit criterion of every subphase must be satisfiable by the subtasks listed under it.
7. Do not include timelines, estimates, or agent assignments — those are the responsibility of the Orchestration Agent.
