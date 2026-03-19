---
name: phased-delivery-planning
description: Organise the development of an application into a sequence of phases
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---
# SKILL: Phased Delivery Planning

## Purpose
Organise the development of an application into a sequence of phases that each deliver a meaningful, testable, and coherent increment of the system — enabling the Orchestration Agent to schedule work in a safe, dependency-respecting order.

## When to Use
- Structuring the output of the Development Plan stage.
- Reviewing or validating a proposed phase structure against the architecture.
- Determining the correct ordering of large blocks of work when circular or ambiguous dependencies exist.

## How to Apply

### 1. Anchor the phase structure to the architecture
Every phase must be justifiable by reference to `architecture-plan.md`. A phase that cannot be traced to a component or layer in the architecture plan is out of scope and must be removed or reclassified.

### 2. Use a layered delivery model as the default starting point
Unless the architecture strongly suggests otherwise, organise phases bottom-up:

| Phase | Typical Content |
|---|---|
| **Foundation** | Infrastructure scaffolding, CI/CD, database schema, configuration management, logging/observability baseline |
| **Core Domain** | Domain entities, aggregates, business logic, internal event model |
| **Application Layer** | Use cases, command/query handlers, service orchestration |
| **API & Integration** | External API surface, third-party integrations, authentication/authorisation |
| **Hardening** | Performance testing, security review, resilience testing, documentation |

Adapt this template to the specific architecture — do not apply it mechanically if the architecture departs from a standard layered model.

### 3. Define a clear objective for each phase
Each phase must have a single sentence objective that describes what the system can do at the end of the phase that it could not do at the start. This is not a task description; it is a capability statement.

Example: "At the end of the Core Domain phase, the system can correctly process double-entry ledger transactions against in-memory state with full invariant validation."

### 4. Ensure each phase is independently verifiable
A phase is only meaningful if its exit criteria can be verified without requiring subsequent phases to be complete. If you cannot test a phase in isolation, it is not a real phase boundary.

### 5. Size phases appropriately
- Phases that are too small (one subphase, one concern) add coordination overhead without value.
- Phases that are too large (more than six or seven subphases) are unmanageable and likely contain hidden phase boundaries.
- If a phase is growing unwieldy, look for a natural seam and split it.

## Rules
1. Every phase must have a capability-oriented objective statement — task-oriented objectives ("build the API") are not acceptable.
2. Every phase must have at least one subphase.
3. Phases must be ordered such that no phase depends on work defined in a later phase — forward dependencies are a planning error.
4. The complete set of phases must cover the entire scope of `architecture-plan.md` — gaps in phase coverage are defects.
5. Do not include timelines, estimates, or resource assignments — those are outside the scope of this plan.
