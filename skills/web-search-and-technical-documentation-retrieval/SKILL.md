---
name: web-search-and-technical-doocumentation-retrieval
description: Locate current, authoritative technical information from the web 
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Web Search and Technical Documentation Retrieval

## Purpose
Locate current, authoritative technical information from the web — including official documentation, API references, engineering specs, and product pages — to ground research and analysis in verified facts rather than assumptions.

## When to Use
- Researching the architecture, infrastructure, or technology choices of an existing product.
- Verifying that a technical claim is accurate and up to date.
- Locating official documentation for a framework, protocol, or service.
- Finding source material to cite in a research or planning document.

## How to Apply

### 1. Formulate a precise query
- Use specific technical terms rather than broad phrases. Prefer "Kafka consumer group rebalancing protocol" over "how Kafka works".
- Include product names, version numbers, or technology names where known.
- Scope the query to the aspect you need: architecture, data model, API design, deployment model, etc.

### 2. Evaluate source quality
Rank sources in this order of preference:
1. Official product documentation or engineering blog (e.g. `engineering.atspotify.com`, `aws.amazon.com/architecture`)
2. Peer-reviewed papers or published conference talks (e.g. VLDB, OSDI, USENIX)
3. Well-attributed technical articles on established platforms (e.g. InfoQ, ACM Queue, High Scalability)
4. Community references only as a last resort — cross-verify any claim from a community source against a primary source

### 3. Retrieve the full page when needed
- Snippet-level results are often insufficient for architectural analysis. Fetch the full document when the snippet lacks the depth required.
- For multi-page documentation, follow internal links to retrieve complete coverage of the topic.

### 4. Record source metadata
For every piece of information retained, record:
- The source URL
- The page title and author/organisation where available
- The retrieval date

### 5. Cross-verify key claims
Any architectural claim that will appear in a final document must be confirmed by at least one additional source or corroborated by a related primary source.

## Rules
1. Never present a retrieved fact as verified if it comes from only one community source (blog comment, forum post, wiki without citations).
2. Always record the source URL alongside each retained fact — undocumented claims must not enter the output document.
3. Do not paraphrase in a way that changes the technical meaning of the original; when in doubt, use a direct short quote with attribution.
4. If a claim cannot be verified through available sources, mark it explicitly as "unverified" rather than omitting or asserting it.
5. Prefer primary sources over aggregators — a company's own engineering blog outranks any third-party summary of it.
