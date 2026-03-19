---
name: adr-authoring
description: Capture significant architectural decisions in a durable, structured format
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: ADR (Architecture Decision Record) Authoring

## Purpose
Capture significant architectural decisions in a durable, structured format so that future agents and developers can understand not just what was decided but why, what was considered, and what the consequences are.

## When to Use
- Recording any architectural decision that has non-trivial consequences or that could reasonably have been made differently.
- Documenting a decision to reject a pattern or approach.
- Capturing a constraint that forces a design choice.
- Revising or superseding a previous decision.

## When NOT to Use
- Trivial implementation details that have no architectural consequence (e.g. variable naming, minor utility function choices).
- Decisions that are fully reversible with no downstream impact.

## ADR Format

Each ADR must use the following structure:

```markdown
## ADR-[NNN]: [Short Decision Title]

**Status:** [Proposed | Accepted | Deprecated | Superseded by ADR-NNN]
**Date:** [YYYY-MM-DD]

### Context
A concise description of the situation, problem, or constraint that required a decision.
Include relevant forces: technical, organisational, regulatory, or operational.

### Decision
A clear, direct statement of what was decided. Use active voice.
"We will use X" not "X was considered".

### Consequences
**Positive:**
- List the benefits or improvements this decision enables.

**Negative / Trade-offs:**
- List the costs, risks, or constraints this decision introduces.

**Neutral:**
- List significant side-effects that are neither clearly positive nor negative.

### Alternatives Considered
For each alternative that was seriously evaluated:
**[Alternative Name]:** One to three sentences on what it is, why it was considered, and the specific reason it was rejected.
```

## How to Apply

### 1. Assign sequential ADR numbers
Start at `ADR-001` and increment by one for each new record. Do not reuse numbers. If a decision is superseded, create a new ADR rather than editing the old one.

### 2. Write the Context before the Decision
Articulate the problem fully before stating the answer. A reader should be able to read the Context and understand why a decision was necessary even if they disagree with the Decision taken.

### 3. Be direct in the Decision section
Avoid hedging language in the Decision. "We may consider using event sourcing" is not a decision. "We will use event sourcing for the Account aggregate" is.

### 4. Be honest in Consequences
List real trade-offs, not just benefits. An ADR with only positive consequences signals that the trade-offs were not thought through.

### 5. Document at least two alternatives for non-trivial decisions
For any significant decision, at least two alternatives must appear. If only one option was ever considered, that is a planning gap, not a legitimate ADR.

## Rules
1. Every significant architectural decision in `architecture-plan.md` must have a corresponding ADR — undocumented decisions are not permitted.
2. ADR status must be kept current — a decision that has been reversed must have its ADR marked "Deprecated" or "Superseded".
3. Do not edit a previously accepted ADR to change its decision — create a new superseding ADR instead.
4. The Decision section must be unambiguous — a reader must not need to infer what was decided from context.
5. All ADRs must be stored alongside `architecture-plan.md` and referenced from it by number.
