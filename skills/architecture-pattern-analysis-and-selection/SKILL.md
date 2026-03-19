---
name: architecture-pattern-analysis-and-selection
description: Evaluate architectural patterns identified in research
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Architecture Pattern Analysis and Selection

## Purpose
Evaluate architectural patterns identified in research against the specific constraints, requirements, and context of the application being developed, and make explicit, justified decisions about which patterns to adopt, adapt, or reject.

## When to Use
- Reading `architecture-research.md` and deciding which patterns are appropriate for the current application.
- Evaluating a proposed design change against the existing architecture.
- Comparing two or more competing patterns for a specific system concern.

## How to Apply

### 1. Establish the evaluation criteria first
Before assessing any pattern, define the dimensions you will evaluate against:
- **Consistency requirements**: strong, eventual, or causal consistency?
- **Scale targets**: expected throughput, data volume, number of tenants?
- **Team constraints**: size of the team, operational complexity that can be sustained?
- **Deployment environment**: cloud, on-premise, hybrid? Managed services available?
- **Compliance and regulatory constraints**: data residency, audit trail requirements, encryption mandates?
- **Domain characteristics**: is the domain event-driven by nature? Does it require long-running transactions?

### 2. Assess each candidate pattern against each criterion
For every pattern under consideration, produce a short structured assessment:
- **What problem does this pattern solve?**
- **What does it assume about the environment?**
- **What does it cost?** (operational complexity, latency, consistency trade-offs)
- **Where has it been observed?** (from the research document)
- **Does it fit our criteria?** — answer each criterion explicitly.

### 3. Make the adoption/rejection decision explicit
Do not leave pattern adoption implicit. For every pattern that will influence the architecture, state:
- **Adopted / Adapted / Rejected**
- The primary reason in one sentence.
- If adapted, describe the adaptation.

### 4. Document inter-pattern dependencies
Some patterns require or preclude others (e.g. event sourcing almost always pairs with CQRS; a strict monolith precludes independent service scaling). Identify and document these relationships.

### 5. Flag uncertainty
If the correct pattern choice is genuinely uncertain given available information, say so explicitly. State what additional information would resolve the uncertainty rather than making a speculative choice.

## Rules
1. Every adopted or rejected pattern must have a documented reason — "we chose microservices" with no justification is not acceptable.
2. Patterns must be evaluated against the actual application context, not in the abstract — a pattern that works at Netflix's scale is not automatically appropriate here.
3. Silence is not a rejection — if a pattern from the research document is not mentioned in the architecture plan, that is a gap, not an implicit decision.
4. Do not select a pattern purely because it is popular or because comparable products use it; the justification must trace back to a specific project requirement or constraint.
5. Uncertainty must be surfaced, not papered over — an honest "this is unresolved" is more valuable than a false confident choice.
