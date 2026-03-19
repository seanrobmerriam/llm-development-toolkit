---
name: inter-agent-coordination
description: Manage the handoffs, communications, and synchronisation points between agents
license: Apache-2.0
metadata:
  author: Sean Merriam
  version: "1.0"
---

# SKILL: Inter-Agent Coordination

## Purpose
Manage the handoffs, communications, and synchronisation points between agents operating within the same execution cycle — specifically the Test Agent ↔ Code Agent cycle and the Code/Test Agent ↔ Orchestration Agent reporting protocol — so that work progresses without ambiguity or duplication.

## When to Use
- The Test Agent notifying the Code Agent that a failing test is ready.
- The Code Agent notifying the Test Agent that an implementation is ready for Green verification.
- Any agent reporting completion, failure, or a blocker to the Orchestration Agent.
- The Orchestration Agent communicating a new subtask instruction to a subagent.

## Coordination Protocols

### Test Agent → Code Agent handoff (Red → Green)
When the Test Agent has produced a failing test and confirmed it is a valid Red state, it must deliver to the Code Agent:
1. The path to the test file(s) containing the new failing test(s).
2. The full test failure output (test name, assertion failure, stack trace if present).
3. A one-sentence description of the behaviour the test is asserting.

The Code Agent must not begin implementation until it has received all three items.

---

### Code Agent → Test Agent handoff (Green confirmation)
When the Code Agent believes the suite is Green:
1. Run the full test suite.
2. Deliver the full test suite output to the Test Agent for independent confirmation.
3. The Test Agent must rerun the suite in its own environment before accepting the Green state.

A Green state is only accepted when both agents have observed a passing suite.

---

### Subagent → Orchestration Agent completion report
When any subagent completes a subtask, it must deliver a structured completion report:

```markdown
## Completion Report: [Subtask ID] — [Subtask Title]

**Status:** Complete | Blocked | Failed

**Files modified:**
- `path/to/file.ext` — description of change

**Test suite result:** All passing | N failures (list below)

**Acceptance criteria met:**
1. [Criterion 1] — Met / Not met
2. [Criterion 2] — Met / Not met

**Blockers / issues encountered:**
[None | Description of blocker with relevant details]

**Escalation required:** Yes / No
[If yes: description of what needs to be escalated and to whom]
```

---

### Orchestration Agent → Subagent instruction delivery
See the `subagent-instruction-composition` skill for the full instruction format. Key coordination rule: the instruction must be fully delivered before the subagent begins work. No verbal or partial instruction is acceptable.

---

## Rules
1. The Code Agent must not write implementation code before receiving the failing test and its failure output from the Test Agent.
2. The Test Agent must not accept a Green state without independently running the suite — trusting the Code Agent's report alone is not sufficient.
3. Completion reports to the Orchestration Agent must use the structured format above — narrative reports without the structured fields are not acceptable.
4. A subagent that encounters a blocker must report it immediately via a completion report with `Status: Blocked` rather than attempting to work around it silently.
5. All coordination between agents is mediated through the Orchestration Agent — direct subagent-to-subagent communication that bypasses the Orchestration Agent is not permitted, except for the Test Agent ↔ Code Agent handoff defined above.
