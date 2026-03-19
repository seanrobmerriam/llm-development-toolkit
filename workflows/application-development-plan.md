# Application Development Plan

1. Read `architecture-plan.md` and all associated ADRs in full before producing any output.
2. Read the PRD and any other available product requirements to understand scope, priorities, and constraints.
3. Decompose the full system into logical development phases — each phase should represent a cohesive, shippable increment of the application (e.g. Foundation, Core Domain, API Layer, Integrations, Hardening).
4. For each phase, define its subphases — each subphase should map to a distinct concern within the phase (e.g. within Foundation: Project Scaffolding, Database Schema, Configuration Management).
5. For each subphase, describe its objective, the components it touches, its entry criteria (what must be true before it starts), and its exit criteria (what must be true before it is considered complete).
6. Identify inter-phase and inter-subphase dependencies and represent them explicitly.
7. Produce the final development plan as `development-plan.md`, structured as a hierarchy of phases and subphases with full criteria and dependency annotations.

## Skills to use
1. Architecture document analysis
2. Scope decomposition and dependency mapping
3. Phased delivery planning
4. Markdown document authoring

## Rules to follow
1. Read `architecture-plan.md` completely before writing the plan — do not plan from assumptions.
2. Every phase must have a clearly stated objective and at least one subphase.
3. Every subphase must have explicit entry and exit criteria — vague criteria ("done when it works") are not acceptable.
4. Dependencies must be stated directionally: "Subphase B depends on Subphase A" is required; implicit ordering is not sufficient.
5. Do not define individual tasks or assign work to agents — that is the responsibility of the Task Plan Agent.
6. The development plan must remain architecture-aligned: if a subphase cannot be traced back to a component in the architecture plan, it does not belong in this document.
