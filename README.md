# Agent Development Pipeline

A structured, multi-agent software development system built around a sequential planning pipeline and a TDD-driven execution pipeline. Drop it into any project, fill in the prompt template, and a coordinated team of AI agents will research, architect, plan, and build your application.

---

## What This Is

This repo provides a complete, reusable framework for orchestrating AI coding agents across the full software development lifecycle — from market research through to working, tested code. It is not tied to any specific AI platform or model. It is a set of documents that tell agents what to do, how to do it, and in what order.

The system is built around three ideas:

- **Read-first discipline** — no agent produces output before reading its inputs in full.
- **Phased gates** — no stage begins before the previous stage's output is verified.
- **One agent, one task** — every atomic unit of work is handled by a single agent in a single lifecycle.

---

## Repository Structure

```
.
├── AGENTS.md                   # Master pipeline document — start here
├── PROMPT-TEMPLATE.md          # Reusable prompt template for any application
├── workflows/                  # Step-by-step workflow files, one per agent role
│   ├── application-architecture-research.md
│   ├── application-architecture-planning.md
│   ├── application-development-plan.md
│   ├── development-task-plan.md
│   ├── development-orchestration-plan.md
│   ├── test-writing.md
│   ├── tdd-coding.md
│   └── debug-error.md
├── skills/                     # How-to skill files referenced by workflow steps
│   ├── web-search-and-documentation-retrieval/SKILL.md
│   ├── engineering-blog-and-whitepaper-analysis/SKILL.md
│   ├── markdown-document-authoring/SKILL.md
│   ├── architecture-pattern-analysis-and-selection/SKILL.md 
│   ├── adr-authoring/SKILL.md
│   ├── system-diagram-and-data-flow-documentation/SKILL.md
│   ├── document-analysis/SKILL.md
│   ├── scope-decomposition-and-dependency-mapping/SKILL.md
│   ├── phased-delivery-planning/SKILL.md
│   ├── work-decomposition-and-atomic-task-scoping/SKILL.md
│   ├── dependency-graph-reasoning/SKILL.md
│   ├── subagent-instruction-composition/SKILL.md
│   ├── output-verification-against-acceptance-criteria/SKILL.md
│   ├── escalation-and-debug-task-generation/SKILL.md
│   ├── inter-agent-coordination/SKILL.md
│   ├── test-framework-operation/SKILL.md
│   ├── failure-message-analysis-and-test-correctness-verification/SKILL.md
│   ├── code-refactoring/SKILL.md
│   ├── source-code-reading-and-interface-analysis/SKILL.md
│   ├── minimum-viable-implementation-authoring/SKILL.md
│   ├── root-cause-analysis-and-stack-trace-interpretation/SKILL.md
│   ├── targeted-bug-fixing/SKILL.md
│   └── regression-verification/SKILL.md
└── rules/                      # Project-level rules (add your own here)
```

---

## How It Works

The system runs in two sequential pipelines.

### Planning Pipeline

Each stage produces a document that the next stage depends on. No stage begins until its input is complete.

```
Research Agent        → architecture-research.md
Architect Agent       → architecture-plan.md + ADRs
Plan Agent            → development-plan.md
Plan Agent            → task-plan.md
```

### Execution Pipeline

Driven by the Orchestration Agent, which reads `task-plan.md` and dispatches every atomic subtask to a fresh subagent.

```
Orchestration Agent
    ├── Code Agent    (one per `code` subtask)
    ├── Test Agent    (one per `test` subtask)
    └── Debug Agent   (one per `debug` subtask)
```

Test and Code Agents work together in a Red → Green → Refactor cycle. Debug Agents reproduce failures, document a root cause hypothesis, apply the minimum fix, and verify the full suite.

---

## Getting Started

### 1. Clone the repo 

```bash
git clone https://github.com/seanrobmerriam/llm-development-toolkit.kit
cd agent-pipeline
```

Or copy the repo contents into the root of your existing project — `AGENTS.md`, `workflows/`, `skills/`, and `rules/` should all sit at the project root.

### 2. Fill in the prompt template

Open `PROMPT-TEMPLATE.md` and replace every `[PLACEHOLDER]` with your application's details:

- Application name and one-sentence description
- Product description and feature areas
- Technical constraints (language, stack, domain-specific rules)
- The domain characteristics that define "comparable" for the research phase
- Any project-specific cross-cutting rules
- Expected output artefacts

Save your filled-in prompt as `PROMPT.md` in the project root.

### 3. Add any project-level rules

If your project has domain-specific rules that every agent must follow (e.g. financial correctness rules, compliance requirements, coding standards), add them as markdown files in the `rules/` directory. Reference them in your `PROMPT.md` and in `AGENTS.md`.

### 4. Run the prompt

Paste the contents of `PROMPT.md` as the initial message to your agent system. The Research Agent will begin the planning pipeline. Each agent hands off to the next.

---

## Agent Roles

| Agent | Workflow | Responsibility |
|---|---|---|
| Research Agent | `application-architecture-research.md` | Researches 5 comparable products and documents their architectures |
| Architect Agent | `application-architecture-planning.md` | Produces the architecture plan and all ADRs |
| Plan Agent | `application-development-plan.md` | Decomposes architecture into phases and subphases |
| Plan Agent | `development-task-plan.md` | Decomposes subphases into atomic tasks and subtasks |
| Orchestration Agent | `development-orchestration-plan.md` | Schedules and dispatches all subtasks; verifies output |
| Test Agent | `test-writing.md` | Writes failing tests (Red), confirms Green, refactors |
| Code Agent | `tdd-coding.md` | Writes minimum implementation to pass tests; refactors |
| Debug Agent | `debug-error.md` | Reproduces failures, documents root cause, applies minimum fix |

---

## Universal Rules

These apply to every agent in every pipeline. The canonical versions live in `AGENTS.md`.

1. **Read-first gate** — every agent reads all input documents and assigned skill files before producing any output.
2. **Single lifecycle, single subtask** — one agent, one task, no exceptions.
3. **No test modification** — Code and Debug Agents never edit test files.
4. **No speculative implementation** — Code Agents write only what the current failing test demands.
5. **Escalation over speculation** — blockers and conflicts are reported to the Orchestration Agent, not worked around.
6. **Pipeline ordering** — no stage begins before its upstream document is complete.

---

## Customising the System

### Adding a new workflow
Create a new markdown file in `workflows/` following the existing format (numbered steps, Skills to use, Rules to follow). Reference it in `AGENTS.md` and in any prompt that needs it.

### Adding a new skill
Create a new markdown file in `skills/` with Purpose, When to Use, How to Apply, and Rules sections. Reference it in the relevant workflow's "Skills to use" list.

### Adding project rules
Add markdown files to `rules/`. These take precedence over `AGENTS.md` in any conflict. Reference them explicitly in your `PROMPT.md`.

### Adapting for a different tech stack
The workflows and skills are stack-agnostic by design. The only stack-specific content lives in the `test-framework-operation` skill, which currently covers Erlang/OTP and Go. Add a new section for your stack there and it will be available to all Test and Debug Agents.

---

## Contributing

Workflow, skill, and rule files should follow the existing format and conventions. Pull requests improving coverage, clarity, or stack support are welcome.

---

## License

See LICENSE.md
