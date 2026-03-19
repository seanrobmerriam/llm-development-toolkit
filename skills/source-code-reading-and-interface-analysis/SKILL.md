---
name: source-code-reading-and-interface-analysis
description: Read and fully comprehend existing source files, module interfaces, type definitions, and public APIs
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Source Code Reading and Interface Analysis

## Purpose
Read and fully comprehend existing source files, module interfaces, type definitions, and public APIs before writing any new code — so that all new implementation integrates correctly with the existing codebase and does not duplicate, contradict, or break existing contracts.

## When to Use
- Before writing any implementation code in a `code` subtask.
- Before writing any test code in a `test` subtask.
- Before applying any fix in a `debug` subtask.
- Any time an agent is instructed to act on a file it has not previously read in the current lifecycle.

## How to Apply

### 1. Read every file listed in the subtask instruction
The Orchestration Agent's instruction will list the target files explicitly. Read each one in full before producing any output. Do not skim — pay particular attention to:
- **Exported function signatures**: name, arity, parameter types, return types.
- **Module attributes and behaviours** (Erlang): `-behaviour(gen_server)`, `-export([...])`, `-type`, `-spec`.
- **Interface definitions and type declarations** (Go): `interface`, `struct`, type aliases, error types.
- **Existing error handling patterns**: how errors are represented and propagated in this codebase.
- **Comments and documentation**: inline comments often capture intent that is not expressed in the code itself.

### 2. Identify the integration points
After reading, map the integration points relevant to your subtask:
- Which existing functions will your new code call?
- Which existing functions will call your new code?
- What types does your code receive and return, and do they match what callers expect?
- Are there existing conventions (naming, error tuple shape, logging pattern) that your code must follow?

### 3. Read related test files
If tests exist for the module you are acting on, read them. Tests document expected behaviour more precisely than prose and reveal edge cases that may not be visible in the implementation.

### 4. Identify potential conflicts before writing
Before writing any code, identify:
- Functions that already exist and must not be duplicated.
- Types that are already defined and must be reused, not redefined.
- Module or package boundaries that must not be crossed.
- Constraints from the architecture plan (read `architecture-plan.md` context from the instruction) that affect the design.

### 5. Build a pre-implementation checklist
Before writing the first line of new code, confirm:
- [ ] All target files have been read in full.
- [ ] All integration point signatures are understood.
- [ ] All relevant existing types are identified.
- [ ] No duplicate implementation already exists.
- [ ] The architecture constraints from the instruction are understood.

## Stack-Specific Notes

### Erlang / OTP
- Always read the `-spec` annotations — they define the contract, not just the implementation.
- Check `-behaviour` declarations to understand which callbacks are required.
- Read the supervision tree configuration to understand how the module fits into the OTP application.
- Check Mnesia table definitions and access patterns before writing any persistence code.

### Go
- Read `interface` definitions before implementing any type that must satisfy them.
- Check `go.mod` for available dependencies before importing anything new.
- Read existing middleware and handler patterns before writing new HTTP handlers.
- Check error wrapping conventions — does this codebase use `fmt.Errorf("%w", err)` or a custom error type?

## Rules
1. No implementation, test, or debug code may be written before all target files have been read in full.
2. Do not duplicate a function or type that already exists — reuse it.
3. New code must follow the existing conventions of the codebase — do not introduce a new pattern without a documented reason.
4. If a required interface or type is missing from the codebase, surface it to the Orchestration Agent rather than inventing it unilaterally.
5. Reading a file partially does not satisfy the read-first gate — the file must be read in full.
