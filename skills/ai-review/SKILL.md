---
name: ai-review
description: "Use when an AI agent is asked to plan, implement, review, approve, or summarize a code change, especially when the request is broad or the generated diff may hide unrelated edits."
---

# AI Review

## Overview

Treat AI output as a proposed change, not as an automatically correct implementation. Require reasoning before editing, keep the scope to one logical change, and inspect the actual diff rather than trusting a summary.

**Core principle:** catch flawed reasoning while it is still a plan, keep each change independently reviewable, and account for every changed line before approval.

**Field-guide coverage:**

10. Read every diff, every time
11. Ask why before what
12. One change per request

## When to Use

Use this skill for every AI-authored code change, review, pull request, patch, refactor, bug fix, or feature implementation. It is especially important when the request is vague, spans several concerns, touches generated or configuration files, or arrives with a confident summary that could hide unrelated edits.

## Portability Rules

- Use the repository's native diff, status, review, and testing tools when available.
- Never claim that a human reviewed or approved a change unless the human explicitly did so.
- Never hide untracked files, generated changes, lockfile updates, formatting churn, failed checks, or uncertainty behind a concise summary.
- If no diff tool or repository state is available, compare before-and-after content directly and state the limitation.
- This skill does not authorize edits; architecture, constraints, permissions, and user approval still apply.

## Mandatory Review Cycle

Every implementation follows this order:

1. **Understand:** restate the problem, desired behavior, evidence, and acceptance criteria.
2. **Explain why:** propose the approach, rationale, alternatives, trade-offs, risks, affected files, tests, and rollback.
3. **Control scope:** reduce the work to one logical, independently testable and reversible change.
4. **Implement:** change only what the approved unit requires.
5. **Inspect:** read the complete actual diff and repository status.
6. **Reconcile:** compare the diff with the plan; remove, split, or explicitly justify surprises.
7. **Verify:** run the agreed checks and inspect actual outcomes.
8. **Present:** provide a review report and leave final acceptance to the human reviewer.

## Habit 11: Ask “Why” Before “What”

Before modifying anything, the agent must explain its reasoning in a reviewable plan.

The plan must include:

- Problem as understood
- Desired behavior and acceptance criteria
- Confirmed facts versus assumptions
- Proposed approach and why it addresses the cause or requirement
- At least one meaningful alternative when a real alternative exists
- Trade-offs and failure modes
- Files, interfaces, data, permissions, and execution paths expected to change
- Tests to run and expected outcomes
- Rollback or containment for risky work
- Questions that block safe implementation

Required format:

```markdown
## Pre-Change Review

### Problem
What is wrong or missing, supported by evidence.

### Why This Approach
Reasoning, constraints, and the causal link between the change and desired result.

### Alternatives
Other credible options and why they are not preferred.

### Expected Scope
- `path/file-a.ts`: purpose of edit
- `path/file-b.test.ts`: behavior to verify

### Risks
Security, compatibility, data, performance, operational, or maintenance risk.

### Verification
Exact checks and expected results.

### Rollback
How the change can be contained or reversed.
```

Rules:

- Do not start implementation with unresolved contradictions or missing high-risk facts.
- Do not disguise a guess as certainty.
- Avoid ceremonial plans that merely repeat the request; reasoning must explain why the approach should work.
- If the plan changes during implementation, stop and revise the scope before continuing.

## Habit 12: One Logical Change Per Request

A logical change has one coherent purpose and can be reviewed, tested, and reverted without depending on unrelated cleanup.

Good unit:

> Reject expired refresh tokens and add focused tests for that behavior.

Bad unit:

> Rewrite authentication, rename services, upgrade dependencies, reformat the repository, and redesign errors.

When a request is broad:

1. Preserve the overall goal.
2. Decompose it into ordered logical changes.
3. Identify dependencies between them.
4. Implement and review one unit at a time.
5. Re-run relevant checks after each unit.
6. Do not bundle “while I am here” refactors, renames, dependency upgrades, or formatting.

Example decomposition:

```markdown
Goal: Fix the whole authentication system.

1. Reproduce and isolate expired refresh-token acceptance.
2. Correct refresh-token validation.
3. Add focused regression tests.
4. Improve the related API error response without changing other errors.
5. Update the client retry behavior in a separate reviewed change.
```

Scope rules:

- Each unit must have its own acceptance criteria.
- Each unit should produce a focused diff.
- If a required prerequisite is discovered, document and review it instead of silently expanding the task.
- Large generated or vendored files should be reviewed separately from authored source.
- If the diff becomes too large to explain line by line, the scope is too large; split it.

## Habit 10: Read Every Diff, Every Time

Never accept a change based only on the agent's summary. Read the actual diff line by line, regardless of how small the request seemed.

A diff is not "reviewed" until checked against the `ai-control` JSONL approval artifact's evidence, scope, constraints, and recorded changes; if no artifact exists, explicitly report a legacy/standalone review with unverified historical approvals and verify current authorization independently instead of claiming artifact-backed review.

Review all of the following:

- Modified, added, deleted, renamed, and untracked files
- Source, tests, configuration, documentation, migrations, schemas, generated files, and lockfiles
- Added and removed control flow
- Error, retry, timeout, cancellation, and fallback behavior
- Authentication, authorization, tenant, role, and data-boundary changes
- Public APIs, data formats, environment variables, feature flags, and defaults
- New or upgraded dependencies and transitive impact
- Logging of secrets, personal data, tokens, or sensitive payloads
- Test assertions, skipped tests, weakened checks, and snapshot changes
- Unrelated formatting, cleanup, comments, or refactors
- Deletions that remove validation, checks, documentation, or error handling

For every changed hunk, answer:

1. Which planned requirement does this satisfy?
2. Why is this exact change necessary?
3. Could a smaller change satisfy the same requirement?
4. What behavior could regress?
5. Which check proves the intended behavior?

If a line cannot be explained, do not approve it. Investigate, remove it, or split it into a separately reviewed change.

## Diff Reconciliation Report

After reviewing, produce:

```markdown
## Diff Review

### Intended Logical Change
One sentence.

### Files Changed
| File | Why It Changed | Planned? | Review Result |
|---|---|---|---|

### Key Behavioral Changes
What execution behavior is different.

### Unexpected or Unrelated Changes
None, or a complete list with disposition.

### Security and Data-Boundary Review
Relevant findings or “not affected,” with reasoning.

### Verification Evidence
Commands or actions and actual outcomes.

### Remaining Risks
Known gaps, unrun checks, or review questions.

### Human Review Required
Exact files or hunks the human should inspect before acceptance.
```

## Stop Conditions

Stop and re-plan when:

- The implementation no longer matches the approved reasoning.
- The diff contains unrelated changes or hidden generated output.
- The request combines multiple independently reviewable purposes.
- A dependency, schema, migration, public API, permission boundary, or destructive edit appears unexpectedly.
- The diff is too large to account for line by line.
- Tests were changed primarily to make failing behavior disappear rather than to express the requirement.
- The agent is asked to approve its own work without exposing evidence and remaining risk.

## Rationalizations to Reject

| Rationalization | Reality |
|---|---|
| “The summary already explains the change.” | Summaries can omit or misdescribe edits; the diff is the record of what changed. |
| “It is only a one-line fix.” | One line can remove authorization, change a default, or corrupt data. Review it. |
| “The formatting changes are harmless.” | Formatting churn hides behavioral edits and increases review error. Separate it. |
| “The refactor was convenient while editing the file.” | Convenience is not scope. Review it as another logical change. |
| “The model is confident.” | Confidence is not evidence, review, or verification. |
| “A large request is faster in one pass.” | Large vague requests create large vague diffs that humans and agents review poorly. |
| “The tests are green, so every changed line is justified.” | Tests do not prove that unrelated or risky changes belong in the diff. |

## Red Flags

- Implementation begins before the reasoning is exposed.
- “Fix everything,” “clean it all up,” or another vague scope remains undecomposed.
- Files outside the expected scope change without explanation.
- Lockfiles, migrations, permissions, or configuration change unexpectedly.
- The review relies on an AI-authored summary instead of the actual diff.
- The agent cannot connect a hunk to an acceptance criterion.
- The agent claims human approval or safe completion on its own authority.

Any red flag means: stop, inspect, narrow, and re-review.

## Completion Gate

Do not recommend acceptance until:

- The rationale was reviewed before implementation.
- The work represents one coherent logical change.
- The complete actual diff and repository status were inspected.
- Every changed hunk is planned, necessary, and explainable.
- Unexpected changes were removed, split, or explicitly reviewed.
- Verification evidence is visible and limitations remain stated.
- A human reviewer has enough information to make the final decision.

**Related skills:** Use `ai-guardrails` before edits involving architecture, constraints, tests, or rollback; use `ai-control` to hand the reviewed state to the next session without losing context.
