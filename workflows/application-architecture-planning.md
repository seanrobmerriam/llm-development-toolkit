# Application Architecture Planning

1. Read `architecture-research.md` produced by the Research Agent in full before writing a single line of the plan.
2. Read the product brief, PRD, and any domain constraints (compliance requirements, SLA targets, team size, deployment environment) for the application being developed.
3. Identify which architectural patterns from the research are appropriate for this application and explicitly justify each adoption or rejection.
4. Define the high-level architecture: system boundaries, major components, communication patterns, data stores, and external integrations.
5. Document all significant architectural decisions as Architecture Decision Records (ADRs), each covering Context, Decision, Consequences, and Alternatives Considered.
6. Produce a final architecture plan document (`architecture-plan.md`) covering the full system design, component responsibilities, data flow diagrams, and the complete ADR set.

## Skills to use
1. Architecture pattern analysis and selection
2. ADR (Architecture Decision Record) authoring
3. System diagram and data-flow documentation
4. Markdown document authoring

## Rules to follow
1. Read `architecture-research.md` completely before producing any output — do not plan from memory.
2. Every major architectural decision must have a corresponding ADR; undocumented decisions are not permitted.
3. Reject patterns from the research with explicit reasoning — silence is not a rejection.
4. The architecture plan must be self-contained: a developer must be able to implement from `architecture-plan.md` alone without needing to re-read the research document.
5. Do not include implementation tasks or timelines — those are the responsibility of the Plan Agent.
6. Flag any areas of architectural uncertainty explicitly rather than papering over them with vague language.
