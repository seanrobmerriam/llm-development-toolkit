---
name: document-analysis
description: Read, comprehend, and extract the structured intelligence needed from pipeline documents
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Document Analysis

## Purpose
Read, comprehend, and extract the structured intelligence needed from pipeline documents (`architecture-research.md`, `architecture-plan.md`, `development-plan.md`, ADRs, etc.) before producing any dependent output. No agent may produce output based on a document it has not read in full.

## When to Use
- Before the Architect Agent begins planning, having received `architecture-research.md`.
- Before either Plan Agent begins their document, having received `architecture-plan.md` and/or `development-plan.md`.
- Before the Orchestration Agent begins scheduling, having received `task-plan.md`.
- Before any agent acts on instructions that reference a source document.

## How to Apply

### 1. Read the entire document before extracting anything
Do not skim, sample, or read only the sections that appear relevant at first glance. Important constraints and dependencies are often in sections that seem unrelated to the task at hand.

### 2. Build a mental model of the document's structure
After reading, reconstruct the document's structure:
- What are the major sections?
- What is the hierarchy of components, phases, tasks, or decisions?
- What are the explicit dependencies and ordering constraints?
- What are the entry and exit criteria (for development plan documents)?

### 3. Extract the specific information relevant to your task
Create a working list of the facts, decisions, and constraints from the document that directly bear on the output you are about to produce. This list is your source of truth — do not rely on memory.

### 4. Identify gaps and ambiguities before proceeding
If the document is incomplete, internally inconsistent, or ambiguous on a point that is critical to your task, flag it to the Orchestration Agent before producing output. Do not silently fill gaps with assumptions.

### 5. Cross-reference related documents
Where multiple documents are specified as inputs (e.g. `architecture-plan.md` and `development-plan.md` together), read both fully and identify any conflicts or inconsistencies between them before proceeding.

### 6. Trace every output claim back to a source document
Every statement in your output that derives from an input document must be traceable to a specific section or item in that document. If a claim cannot be traced, it is an assumption and must be marked as such or removed.

## Rules
1. Reading a document partially does not satisfy the read-first gate — the document must be read in full.
2. Do not begin producing output until all required input documents have been read completely.
3. Assumptions introduced because a document was ambiguous must be documented explicitly in the output — silent assumptions are not permitted.
4. If two input documents contradict each other, raise the conflict to the Orchestration Agent rather than choosing one silently.
5. Document analysis is not a one-time step — if a document is updated during the pipeline, re-read the updated version before producing any further output that depends on it.
