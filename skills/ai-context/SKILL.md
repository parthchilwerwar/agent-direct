---
name: ai-context
description: Use when an AI agent starts, resumes, debugs, or implements a non-trivial code change that spans files, functions, modules, people, or sessions, or when another human or agent must continue the work later.
---

# AI Context

## Overview

Keep project context next to the code instead of depending on chat memory. The written record must let a new human or AI agent understand the current state, the reasoning behind decisions, the intent of non-obvious logic, the execution path, and the complete history of the active bug or feature.

**Core rule:** No meaningful code change may depend only on information that exists inside the current conversation.

**This skill covers:**

1. `Handover.md` — where things stand
2. `Decisions.md` — why, not just what
3. Explicit comments — inline intent
4. `Flow.md` — how execution travels
5. `Bug.md` or `Feature.md` — start to finish

## When to Use

Use this skill for features, bug fixes, refactors, integrations, migrations, multi-file changes, unfamiliar repositories, long-running work, and any task likely to continue in another session. A trivial typo or purely mechanical formatting edit does not need a full task trace, but it must not contradict existing documentation.

## File Placement

First follow the repository's existing documentation convention. Do not create parallel files that duplicate an established system.

When no convention exists:

- Keep project-wide `Handover.md`, `Decisions.md`, and `Flow.md` at the repository root.
- Keep the active task trace in `docs/ai-work/<task-slug>/Bug.md` or `docs/ai-work/<task-slug>/Feature.md`.
- Keep names stable. Do not silently rename or scatter these records.

Never place secrets, tokens, credentials, private customer data, or confidential prompt contents in these files.

## Operating Workflow

1. **Read before acting.** Read the current handover, relevant decisions, execution flow, and active bug or feature trace before proposing edits.
2. **Verify against the repository.** Documentation may be stale. Inspect the actual code, configuration, branch, and current diff before treating a statement as fact.
3. **State uncertainty.** Mark unverified claims as `Unknown`, `Unverified`, or `Inferred`. Never invent architecture, root causes, test results, or completed work.
4. **Update incrementally.** Record meaningful changes while working rather than reconstructing them from memory at the end.
5. **Reconcile at completion.** Before declaring the task complete, make every document agree with the final code, actual diff, and actual verification results.

## 01 — `Handover.md`: Current State

### Purpose

`Handover.md` is a living snapshot of where the project or active task stands now. It is not a transcript, diary, changelog, or dump of every conversation.

A new session must be able to answer:

- What is done?
- What is in progress?
- What is broken or blocked?
- What must be avoided?
- What is the exact next step?
- What has and has not been verified?

### Required Template

```markdown
# Handover

**Last updated:** <ISO-8601 timestamp>
**Active task:** <task name or issue ID>
**Branch / revision:** <branch and commit, or unavailable>
**Overall status:** <not started | in progress | blocked | ready for review | complete>

## Done
- <completed fact with file or commit reference>

## In Progress
- <unfinished work and its current state>

## Broken or Blocked
- <failure, blocker, owner, and evidence>

## Avoid / Preserve
- <known trap, constraint, invariant, or area not to touch>

## Next Exact Step
- <one concrete action the next session should perform first>

## Verification State
- Passed: <checks actually run>
- Failed: <checks that failed>
- Not run: <checks still required and why>
```

### Update Rules

- Replace stale status instead of endlessly appending history.
- Use concrete facts such as file names, commands, errors, and issue IDs.
- Keep failed approaches in the task trace, not as clutter in the current-state summary.
- If the current code contradicts the handover, correct the handover before continuing.

## 02 — `Decisions.md`: Rationale

### Purpose

Record every meaningful technical decision and the reasoning behind it. Code and Git history show what changed; this file preserves why the chosen approach was accepted.

A decision is meaningful when it affects architecture, dependencies, public interfaces, data models, security, performance, maintainability, operational behavior, or future implementation choices. Do not record trivial formatting choices.

### Required Decision Record

```markdown
## DEC-<number>: <decision title>

**Date:** <ISO-8601 date>
**Status:** <proposed | accepted | superseded | rejected>
**Related task:** <issue, Bug.md, or Feature.md path>
**Agent context:** <agent/model/version if known>

### Context
<Problem, constraints, evidence, and forces that required a decision.>

### Decision
<The exact choice that was made.>

### Why
<Why this option fits the evidence and constraints.>

### Alternatives Considered
- <alternative>: <why it was not selected>

### Trade-offs and Consequences
- Benefit: <expected benefit>
- Cost: <accepted downside>
- Risk: <remaining risk>

### Validation
<How the decision will be checked in code, tests, review, or production.>
```

### Decision Rules

- Record rationale, not only outcome.
- Do not rewrite history. Mark an old decision `superseded` and link the replacement.
- Separate facts from assumptions.
- Tie decisions to evidence, constraints, affected files, and verification.

## 03 — Explicit Comments: Inline Intent

### Purpose

Comment non-obvious logic while writing it. Comments must explain intent and context, not translate syntax into English.

A useful comment answers one or more of these questions:

- Why does this block exist?
- What calls it or depends on it?
- Which invariant or assumption must remain true?
- Why is the obvious alternative unsafe?
- What could break if this behavior changes?

### Good Example

```ts
// Keep tenantId in the repository query even though the HTTP caller is
// authenticated. Background jobs use this repository without request-scoped
// RLS, so removing the predicate can expose records across tenants.
return orders.findMany({ where: { tenantId, status: 'open' } });
```

### Bad Example

```ts
// Find open orders.
return orders.findMany({ where: { tenantId, status: 'open' } });
```

### Comment Rules

- Comment intent, invariants, security boundaries, unusual trade-offs, and cross-module dependencies.
- Do not comment obvious assignments, loops, or function calls.
- Update or remove comments when behavior changes. A stale comment is worse than no comment.
- Do not use comments to excuse confusing code that should be simplified.
- Comments support tests and documentation; they do not replace them.

## 04 — `Flow.md`: Execution Trace

### Purpose

Document how execution actually travels between files, functions, modules, services, data stores, and external systems. Bugs often exist at boundaries, so the flow must show what calls what, in what order, and which part is being changed.

### Required Template

```markdown
# Flow

**Scope:** <feature, bug, route, job, command, or event>
**Last verified:** <timestamp and revision>

## Entry Point
- `<file>:<symbol>` — <what initiates the flow>

## Execution Path
1. `<file>:<symbol>` — <input and responsibility>
2. `<file>:<symbol>` — <validation, transformation, or decision>
3. `<file>:<symbol>` — <data access or external call>
4. `<file>:<symbol>` — <response, event, or side effect>

## Data Movement
- Input: <shape and source>
- Transformations: <important changes>
- Persistence: <writes, reads, transactions>
- Output: <shape and destination>

## Boundaries and Side Effects
- Authentication / authorization: <where enforced>
- External systems: <API, queue, storage, email, payment, etc.>
- Errors / retries: <where handled>

## Current Modification
- Changed nodes: <exact files and symbols>
- Expected impact: <what behavior changes>
- Must remain unchanged: <protected behavior>

## Unknowns
- <unverified path, caller, or side effect>
```

### Flow Rules

- Trace the real path from repository evidence; do not guess from naming alone.
- Use exact file and symbol names where available.
- Include asynchronous jobs, events, middleware, hooks, queues, callbacks, and side effects.
- Update only the affected flow, but preserve enough surrounding context to show impact.

## 05 — `Bug.md` or `Feature.md`: Full Task Trace

### Purpose

Keep one task file that traces the work from discovery or scope through implementation and verification. Another human or AI agent must be able to read it cold and continue without replaying the original conversation.

### Required Template

```markdown
# <Bug or Feature>: <title>

**ID:** <issue or task ID>
**Owner:** <human or team, if known>
**Status:** <scoped | investigating | implementing | verifying | blocked | complete>
**Started:** <timestamp>
**Last updated:** <timestamp>

## How It Was Found or Scoped
<Report, request, incident, evidence, or business need.>

## Expected Behavior
<Observable acceptance criteria.>

## Actual Behavior
<Observed behavior, error, logs, reproduction, or current limitation.>

## Scope
- In scope: <one logical outcome>
- Out of scope: <explicit exclusions>

## Reproduction or Acceptance Procedure
1. <step>
2. <step>

## Investigation and Root Cause
<Evidence-based explanation. Mark hypotheses as hypotheses.>

## Attempts
| Attempt | Result | Evidence | Keep / Reject |
|---|---|---|---|
| <approach> | <worked or failed> | <test, log, or diff> | <decision> |

## Chosen Plan
<Approach, rationale, affected files, risks, and rollback reference.>

## Implementation
- `<file>:<symbol>` — <what changed and why>

## Verification
| Check | Command or procedure | Expected | Actual | Status |
|---|---|---|---|---|
| <check> | `<command>` | <expected> | <actual> | <pass/fail/not run> |

## Remaining Work and Risks
- <open item, limitation, monitoring need, or follow-up>
```

### Task Trace Rules

- Preserve failed attempts and why they failed; do not repeat them in later sessions.
- Separate root-cause evidence from speculation.
- Record the final implementation only after inspecting the actual diff.
- A task is not complete while required verification is `Not run` without explicit acceptance of that limitation.

## Common Failures

| Failure | Correction |
|---|---|
| Copying the entire chat into documentation | Keep only durable state, reasoning, evidence, and next actions. |
| Writing docs before inspecting the code | Verify the repository first and mark unknowns. |
| Recording every tiny choice in `Decisions.md` | Record only decisions with future consequences. |
| Commenting what the code already says | Explain intent, assumptions, callers, and failure risks. |
| Drawing a theoretical flow | Trace actual files, symbols, data, and side effects. |
| Declaring a bug fixed without evidence | Record commands, expected outputs, actual outputs, and status. |

## Completion Gate

Do not call the documentation complete until a cold reader can answer all of the following without the original chat:

1. Where does the work stand now?
2. Why were the important choices made?
3. What non-obvious logic must be preserved?
4. How does execution travel through the affected system?
5. How was the bug or feature investigated, implemented, and verified?

If any answer is missing, update the relevant artifact before handoff.

**Related skills:** Use `ai-guardrails` for architecture, constraints, proof, and rollback; use `ai-control` for the final session handoff, context pin, and mental-model check.
