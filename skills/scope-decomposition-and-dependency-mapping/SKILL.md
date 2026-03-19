---
name: scope-decomposition-and-dependency-mapping
description: Break down a large, complex scope (a full application architecture) into a set of coherent, independently deliverable phases and subphases
license: Apache 2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Scope Decomposition and Dependency Mapping

## Purpose
Break down a large, complex scope (a full application architecture) into a set of coherent, independently deliverable phases and subphases, and make all ordering dependencies between them explicit so they can be correctly sequenced by downstream agents.

## When to Use
- Decomposing `architecture-plan.md` into a phased development plan.
- Identifying which work blocks depend on other work blocks being complete.
- Validating that a proposed decomposition has no circular dependencies and no orphaned components.

## How to Apply

### 1. Identify the natural seams in the architecture
Read `architecture-plan.md` and look for boundaries where components can be built and tested independently:
- Infrastructure and persistence layers are almost always the foundation phase.
- Domain logic depends on the data layer but not on the API layer.
- API and integration layers depend on domain logic.
- Hardening, observability, and performance work typically comes last.

### 2. Define phases at the right granularity
A phase should represent a cohesive, deployable increment — not a single class and not the entire application. A useful test: "if this phase were the last thing we built, would the application have meaningful functionality?" If yes, it is appropriately sized.

### 3. Define subphases within each phase
Each subphase should map to a distinct, bounded concern within the phase. Subphases should be small enough that a team can complete one in isolation without needing to simultaneously work on another subphase.

### 4. Write explicit entry and exit criteria for each subphase
- **Entry criteria**: the conditions that must be true before work on this subphase can begin (e.g. "The Mnesia schema migration for accounts is complete and reviewed").
- **Exit criteria**: the conditions that must be true before this subphase is considered done (e.g. "All account CRUD handlers return correct responses and pass integration tests").
- Criteria must be verifiable — "it works" is not a criterion.

### 5. Map dependencies directionally
For every subphase, list each subphase it depends on. State the dependency explicitly:
- `Subphase 2.1 (Domain Logic: Account Aggregate) depends on Subphase 1.2 (Mnesia Schema)`

### 6. Validate the dependency graph
Before finalising the plan:
- Check for circular dependencies — if A depends on B and B depends on A, the decomposition is wrong.
- Check for orphaned subphases — every subphase must connect to at least one other via a dependency or be the explicit starting point.
- Check for completeness — every component in `architecture-plan.md` must map to at least one subphase.

## Rules
1. Every subphase must have both entry criteria and exit criteria — partial criteria are not acceptable.
2. Dependencies must be stated directionally and explicitly — implicit sequencing ("obviously A comes before B") is not sufficient.
3. The decomposition must be architecture-complete — every component in `architecture-plan.md` must be traceable to at least one subphase.
4. Circular dependencies indicate a decomposition error that must be resolved before the plan is finalised.
5. Do not include task-level detail (individual functions, files, or test cases) in the phase/subphase decomposition — that is the responsibility of the Task Plan Agent.
