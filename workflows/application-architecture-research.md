# Application Architecture Research

1. Read the product brief, PRD, or any available description of the application being developed to fully understand its domain, purpose, and intended feature set.
2. Identify the five most comparable products currently available in the market — prioritise products that share the same domain, scale, and technical complexity.
3. For each product, research and document its publicly known architecture: infrastructure, core services, data layer, API design patterns, and any notable architectural decisions.
4. Identify architectural patterns that appear across multiple products (e.g. event sourcing, CQRS, microservices, monorepo, saga orchestration) and note their prevalence.
5. Produce a structured research document (`architecture-research.md`) with one clearly formatted section per product, followed by a cross-cutting patterns summary.

## Skills to use
1. Web search and technical documentation retrieval
2. Engineering blog and whitepaper analysis
3. Markdown document authoring

## Rules to follow
1. Document exactly five products — no more, no fewer.
2. Every architectural claim must be traceable to a public source (blog post, docs page, conference talk, paper); include a reference link for each claim.
3. Do not speculate about proprietary internals — clearly mark anything inferred as "inferred" rather than stated fact.
4. Keep each product section to the same structure: Overview, Infrastructure, Data Layer, API Design, Notable Decisions, Sources.
5. The final cross-cutting summary must explicitly call out patterns relevant to the product being developed.
