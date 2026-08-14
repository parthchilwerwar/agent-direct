---
name: ai-control
description: Use when ending or resuming an AI coding session, switching agents or model versions, debugging an earlier AI decision, or deciding whether a human understands a change well enough to accept it.
---

# AI Control

## Overview

Preserve continuity and accountability across sessions, tools, and model versions. End every session with a useful handoff, record which agent context influenced meaningful work, and refuse acceptance until the human owns the mental model.

**Core rule:** Sessions may end and models may change, but project understanding and responsibility must not disappear with them.

**This skill covers:**

13. Handoff summary, every session
14. Version-pin your context
15. Own the mental model

## When to Use

Use this skill at the start and end of every meaningful AI-assisted coding session, before switching models or agents, when investigating older AI-generated work, and immediately before a human accepts a change.

## Start-of-Session Protocol

1. Read the latest `Handover.md`, active `Bug.md` or `Feature.md`, relevant decisions, constraints, flow, test checklist, and rollback plan.
2. Compare the handoff with the actual repository state, branch, revision, working tree, and open failures.
3. Report contradictions before making changes. Do not continue from a stale handoff as though it were current.
4. Record the active agent and context information described below.
5. Restate the exact next action and protected boundaries before implementation.

## 13 — Session Handoff Summary

### Purpose

End every session with a short note that prevents the next session from starting cold. The handoff should capture durable state, not conversation history.

### Mandatory Five-Line Handoff

Use exactly these five fields for the compact session summary:

```markdown
1. Completed: <what was finished, with files or revision>
2. Current state: <what now works, fails, or remains partially implemented>
3. Remaining: <unfinished work or unanswered question>
4. Watch-outs: <risk, constraint, failed approach, or area to avoid>
5. Next action: <one precise first step for the next session>
```

Add this summary to `Handover.md` and update the longer current-state sections when necessary.

### Handoff Rules

- Write it at the end of every meaningful session, even when no code was completed.
- Use facts, paths, commands, errors, and revision identifiers.
- State failed and unrun checks openly.
- Do not write vague lines such as “continue debugging” or “finish the rest.”
- Keep the five-line summary concise; put investigation history in the task trace.
- If the session changed nothing, say what was inspected, what was learned, and why work is blocked.

## 14 — Version-Pin the Context

### Purpose

Record which AI model, agent, tool, and repository context influenced a meaningful decision or change. “The AI did this” is not enough when a project uses multiple models over time.

### Required Context Record

```markdown
## Agent Context

**Timestamp:** <ISO-8601 timestamp>
**Agent / product:** <name or unavailable>
**Model:** <exact model identifier or unavailable>
**Model version / build:** <version, snapshot, or unavailable>
**Tool / IDE / runtime:** <coding agent, editor, CLI, or orchestration layer>
**Task:** <issue or task ID>
**Decision records:** <related DEC IDs>
**Branch:** <branch or unavailable>
**Revision before:** <commit or unavailable>
**Revision after:** <commit or working-tree state>
**Instruction sources:** <project instructions and loaded skill names/versions>
**Relevant environment:** <language, runtime, dependency, database, or platform versions>
**Known limitations:** <missing tools, unavailable tests, context gaps>
```

### Version-Pinning Rules

- Record exact identifiers when exposed by the environment; otherwise write `Unavailable`. Never guess a model or version.
- Link the context record to meaningful decisions, task traces, and revisions rather than creating an isolated log.
- Record context again after switching models, agents, branches, or major instruction sets.
- Do not rely on a product label alone when an exact model identifier is available.
- Pin repository and environment context as well as the model because behavior depends on both.
- Do not include secrets, hidden system prompts, credentials, or private data.

## 15 — Own the Mental Model

### Purpose

Documentation supports understanding; it does not replace it. A human is not ready to accept AI-generated code unless they can explain what it does in their own words.

### Mandatory Teach-Back

Before recommending acceptance, the agent must provide a plain-language explanation that covers:

1. The original problem and evidence.
2. Why the chosen approach addresses the cause.
3. The execution path from entry point to output or side effect.
4. The data read, transformed, written, emitted, or exposed.
5. The important invariants, permissions, trust boundaries, and assumptions.
6. The files and interfaces changed, including upstream and downstream impact.
7. The main failure modes and remaining risks.
8. The verification actually performed and what remains untested.
9. The rollback or recovery path.

Then ask the human reviewer to explain the same change back in their own words or answer targeted questions. Do not treat silence, a generic “looks good,” or an AI-generated summary as proof of understanding.

### Mental-Model Questions

The reviewer should be able to answer:

- What causes this code path to run?
- Which components and data are involved?
- Why was this implementation selected over the alternatives?
- Which security, permission, data, or compatibility boundary must remain true?
- What observable behavior proves the change works?
- What could still fail?
- How would we reverse it safely?

If any answer is unclear, inspect the relevant code, diff, flow, decision, test, or rollback record before acceptance.

### Acceptance Rule

```text
Cannot explain the change → not ready to accept it.
Can repeat the AI summary but cannot trace the code → not ready to accept it.
Documentation is complete but understanding is missing → not ready to accept it.
```

The human owns the final decision. The agent's role is to make the implementation inspectable and explainable, not to manufacture confidence.

## End-of-Session Protocol

1. Inspect the actual diff and repository state.
2. Update the active task trace with work performed, failed attempts, and verification.
3. Update decisions, flow, constraints, tests, and rollback when affected.
4. Add the mandatory five-line handoff to `Handover.md`.
5. Record the current agent/model/revision context.
6. State whether the task is complete, blocked, or ready for human review.
7. Perform the mental-model explanation before requesting acceptance.

## Common Failures

| Failure | Correction |
|---|---|
| Ending with “done” and no handoff | Write the five-line summary with concrete state and next action. |
| Copying the whole session into `Handover.md` | Preserve current state; move detailed history to the task trace. |
| Guessing the model or version | Use the exact identifier or `Unavailable`. |
| Recording only the model name | Also record task, instructions, branch, revisions, environment, and limitations. |
| Treating documentation as understanding | Require a plain-language teach-back tied to actual code and evidence. |
| Accepting because tests pass | Explain execution, boundaries, risks, and rollback in addition to test evidence. |
| Starting from an old handoff without checking Git | Reconcile handoff and repository state first. |

## Red Flags — Stop Before Acceptance

- The next session cannot identify one precise first action.
- The handoff hides failures or unrun tests.
- The model/version record is guessed or missing for a meaningful decision.
- The reviewer can describe the outcome but not the execution path.
- The reviewer cannot identify changed data, permissions, side effects, or rollback.
- The explanation depends on “the AI said so.”
- Documentation and the actual diff disagree.

Any red flag means the work is not ready for acceptance or handoff.

## Completion Gate

Session control is complete only when:

- The next session can resume without re-explaining the project.
- Agent, model, repository, instruction, and environment context are recorded without guessing.
- The actual repository state matches the handoff.
- The human reviewer can explain the change independently of the AI summary.
- Remaining risks, untested areas, and the recovery path are explicit.

**Related skills:** Use `ai-context` to maintain the persistent handover, decisions, comments, flow, and task trace; use `ai-review` before requesting human acceptance.
