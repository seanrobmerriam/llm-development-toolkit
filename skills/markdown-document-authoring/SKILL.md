---
name: markdown-document-authoring
description: Produce well-structured, consistently formatted markdown documents
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Markdown Document Authoring

## Purpose
Produce well-structured, consistently formatted markdown documents that serve as durable, machine-readable, and human-readable artefacts that can be consumed by both agents and developers throughout the pipeline.

## When to Use
- Producing any pipeline output document (`architecture-research.md`, `architecture-plan.md`, `development-plan.md`, `task-plan.md`, etc.).
- Writing ADRs, skill files, workflow files, or any reference document.
- Authoring structured content that will be read by a downstream agent.

## How to Apply

### 1. Use the prescribed document structure
Every pipeline document has a defined structure. Read the relevant workflow file to identify the required sections before writing. Do not invent sections or omit required ones.

### 2. Lead with a document header block
Every document must open with:
- A top-level `#` heading with the document name.
- A brief one-to-two sentence description of the document's purpose.
- A "Produced by" and "Consumed by" line identifying the producing and consuming agents.

### 3. Apply heading hierarchy consistently
- `#` — Document title only.
- `##` — Major sections (e.g. a product in the research document, a phase in the development plan).
- `###` — Subsections within a major section (e.g. Data Layer within a product section).
- `####` — Detail items within a subsection. Do not go deeper than four levels.

### 4. Use tables for structured comparisons
When presenting multiple items with the same attributes (e.g. products with the same research fields, subtasks with type and dependency annotations), use a markdown table rather than prose repetition.

### 5. Use code blocks for technical content
Wrap all commands, file paths, code snippets, module names, and configuration values in backticks or fenced code blocks. Do not embed technical identifiers in raw prose.

### 6. Use definition lists or bold-label paragraphs for keyed content
For ADRs, criteria, and annotations, use the pattern:
```
**Context:** ...
**Decision:** ...
**Consequences:** ...
```
This makes the fields machine-parseable by downstream agents.

### 7. End every document with a revision note
Close every document with:
```
---
_Last updated by: [Agent Name]_
```

## Rules
1. Every required section from the workflow specification must be present — do not omit sections because they seem short or obvious.
2. Do not use HTML tags inside markdown pipeline documents.
3. Links must use the standard markdown format `[text](url)` — bare URLs are acceptable only in source reference lists.
4. Heading text must be unique within a document — duplicate headings break anchor navigation and confuse downstream agents.
5. Do not use emphasis (bold/italic) for decoration; use it only to mark genuinely critical information or defined terms.
6. All documents must be valid UTF-8 with Unix line endings.
