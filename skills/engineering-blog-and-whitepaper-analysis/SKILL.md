---
name: engineering-blog-and-whitepaper-analysis
description: Extract accurate, structured architectural intelligence from long-form technical sources
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Engineering Blog and Whitepaper Analysis

## Purpose
Extract accurate, structured architectural intelligence from long-form technical sources — including company engineering blogs, academic whitepapers, conference papers, and published post-mortems — and translate that intelligence into citable, usable research findings.

## When to Use
- Analysing how a company built or evolved a major system component.
- Extracting architectural decisions, trade-offs, and lessons learned from a published case study.
- Understanding the rationale behind a technology choice made by a comparable product.
- Building a cross-product patterns summary from multiple long-form sources.

## How to Apply

### 1. Skim for relevance before deep reading
- Read the abstract, introduction, section headings, and conclusion first.
- Determine whether the document addresses architecture, data model, scalability, consistency, or another specific dimension before committing to a full read.
- Discard documents that are primarily marketing content with no substantive technical detail.

### 2. Extract the architectural signal
When reading in depth, focus on:
- **Design decisions**: what was chosen and what was explicitly rejected.
- **Scale and constraint context**: at what scale does this architecture operate? What constraints drove the choices?
- **Data layer**: storage engines, consistency models, partitioning strategies.
- **Communication patterns**: synchronous vs asynchronous, event-driven, RPC, etc.
- **Failure handling**: how the system degrades, recovers, or isolates faults.
- **Evolution notes**: what changed over time and why — this often reveals more than the original design.

### 3. Distinguish stated facts from inferred conclusions
- **Stated**: explicitly written in the source ("We use a single-leader replication model").
- **Inferred**: drawn from context or implication ("The use of Zookeeper suggests leader election is centralised").
- Label all inferences as such in your notes and output documents.

### 4. Note the publication date and version context
Architecture described in a 2016 blog post may not reflect the current system. Always note the publication date and flag whether the information is likely still current.

### 5. Summarise per source before synthesising
Produce a short structured summary for each document before attempting cross-document synthesis. This prevents conflation of details from different sources.

## Rules
1. Every extracted claim must be attributed to a specific source with a URL or citation — do not pool findings from multiple sources into a single unattributed assertion.
2. Inferred architectural conclusions must be labelled "inferred" in all output documents.
3. Do not treat a company's marketing content, press releases, or sales documentation as architectural evidence.
4. Publication date must be recorded; claims from documents older than three years should be flagged as potentially outdated.
5. If a whitepaper or blog post contradicts another source about the same product, document both versions and the discrepancy rather than picking one silently.
