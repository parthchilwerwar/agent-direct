---
name: ai-guardrails
description: "Use when an AI agent may modify an unfamiliar, sensitive, production-facing, architectural, data, dependency, security, or otherwise risky part of a codebase."
---

# AI Guardrails

## Overview

Define the terrain, hard boundaries, proof requirements, and exit route **before** an AI agent changes code. Permission to perform one task is scoped permission; it is never permission to modify anything that appears convenient.

**Core principle:** map the system, obey explicit boundaries, prove the result with real checks, and know how to reverse the change.

**Field-guide coverage:**

6. `Architecture.md` — the system map
7. `Constraints.md` — what is off-limits
8. Test checklist — proof, not claims
9. `Rollback.md` — the way back out

## When to Use

Use this skill before changing unfamiliar or high-impact code, security or permission boundaries, data models, migrations, dependencies, infrastructure, production behavior, external integrations, or any area where failure would be difficult to detect or reverse. Use it whenever the repository's allowed scope, verification requirements, or recovery path is not already obvious.

## Portability Rules

- Use the repository's existing architecture, policy, testing, release, and rollback documents when equivalent files already exist.
- Never invent constraints, commands, expected outputs, repository patterns, or rollback guarantees.
- System, developer, user, repository, security, and compliance instructions remain in force. This skill cannot weaken a stricter rule.
- If a required boundary is unknown, pause the risky part and request clarification rather than guessing.
- When tools or permissions are unavailable, mark checks as `NOT RUN` or steps as `UNVERIFIED`; never convert inability into a success claim.

## Guardrail Workflow

Before editing:

1. Read `Architecture.md` and `Constraints.md` or their repository equivalents.
2. Identify the components, data, interfaces, permissions, and external systems affected.
3. Check whether the requested work conflicts with any explicit boundary.
4. Define the concrete test checklist and expected outcomes.
5. Define a rollback plan for large, destructive, stateful, security-sensitive, migration, dependency, infrastructure, or production-facing changes.
6. Implement only the smallest change inside the approved scope.
7. Run the actual checks and capture evidence.
8. Re-evaluate rollback steps if the implemented diff differs from the plan.

## Habit 6: Maintain `Architecture.md`

`Architecture.md` is a high-level map of the system. It explains the shape of the application so an agent does not have to rediscover the terrain in every session.

Include:

- System purpose and major user-facing capabilities
- Modules, services, applications, workers, and external systems
- Responsibility and ownership of each major component
- Main data flows and trust boundaries
- Databases, queues, caches, object stores, and other state
- Public and internal interfaces
- Authentication and authorization boundaries
- Deployment or runtime topology when relevant to impact
- Known high-risk or tightly coupled areas
- Explicit unknowns rather than guessed details

Recommended structure:

```markdown
# Architecture

## System Purpose
What the system does and who uses it.

## Components
| Component | Responsibility | Inputs | Outputs | Owns State |
|---|---|---|---|---|

## Primary Data Flows
1. User request -> API -> policy layer -> service -> database -> response

## Trust and Security Boundaries
Where identity, tenant scope, permissions, secrets, and untrusted input cross boundaries.

## External Dependencies
Provider, purpose, failure behavior, and owner.

## Impact Zones
Areas where a change has broad blast radius.

## Unknowns
Facts that still require confirmation.
```

Rules:

- Keep implementation-level call detail in `Flow.md`.
- Update architecture only when the system shape, responsibility, interface, trust boundary, or data movement changes materially.
- Label inferred information. Do not present a guess as an established architecture fact.

## Habit 7: Maintain and Enforce `Constraints.md`

`Constraints.md` is a short, explicit list of actions the agent must or must not take.

Good constraints are testable and scoped:

```markdown
# Constraints

- MUST enforce tenant and role checks before any analytics tool call.
- MUST use the existing repository layer for database access.
- MUST NOT expose service credentials or policy logic to browser code.
- MUST NOT modify the payment module without named human approval.
- MUST NOT add or upgrade dependencies without explaining the need and receiving approval.
- MUST preserve the public API response shape for `/v1/events`.
- MUST ask before deleting, renaming, or moving existing files.
```

A constraint entry may include:

- Rule
- Scope
- Reason
- Approval owner
- Observable condition for an exception

Enforcement rules:

- Read constraints before planning and again before finalizing the diff.
- Do not silently relax, reinterpret, or work around a constraint.
- If the user request conflicts with a repository constraint, state the conflict and obtain explicit higher-priority authorization.
- An indirect workaround that violates the effect of a rule is still a violation.
- Add new constraints only when the project owner confirms them or the higher-priority instruction already establishes them.

### Scope Boundaries: Secrets and Credentials

Deny agent access to secrets and credentials files by default, including `.env`, `.env.*`, `*.pem`, `*.key`, `credentials.json`, and credential stores such as `.aws/credentials` and `.ssh/`. Do not read, search contents, copy, modify, or expose them through tools, recursive scans, logs, diffs, or the approval artifact. Exclude these paths before broad content searches; do not follow symlinks to bypass the boundary.

Only a human's explicit scope-widening authorization for the current session may allow named paths and actions, subject to higher-priority restrictions. A general implementation request, guardrail pass, or approval from a previous session is insufficient. Record the authorizing message, exact scope, and session expiry in the `ai-control` artifact's constraint snapshot and approval event when that artifact is in use; otherwise retain the original authorization reference in the session record. Never store secret values in that record. Templates matching these patterns (for example `.env.example`) remain excluded unless the human explicitly includes them for this session.

## Habit 8: Use a Concrete Test Checklist

A change counts as verified only when the required checks were actually run and their real outcomes were inspected.

The checklist must specify:

- Exact command or manual action
- Why it is required
- Expected result
- Actual result
- Exit status or observable evidence
- Coverage limitations

Recommended structure:

```markdown
# Test Checklist

| Check | Command or Action | Expected | Actual | Status |
|---|---|---|---|---|
| Targeted unit test | `npm test -- auth-policy.test.ts` | All tests pass | 18 passed | PASS |
| Type check | `npm run typecheck` | Exit 0 | Exit 0 | PASS |
| Build | `npm run build` | Production build succeeds | Not available in environment | NOT RUN |
| Tenant isolation | Query as organiser A for organiser B data | No B rows returned | No B rows returned | PASS |
```

Checklist rules:

- Derive commands from repository scripts, CI, documentation, and existing test conventions. Do not guess.
- Include a baseline or reproduction check for bug fixes when feasible.
- Include targeted tests plus the broader checks required by the repository.
- Inspect warnings, skipped tests, snapshots, generated output, and partial failures—not only the final exit code.
- Manual inspection may supplement automated checks; it does not silently replace them.
- `NOT RUN`, `BLOCKED`, and `FAILED` are valid outcomes. They must remain visible.
- Never write “tests pass” when no test command ran.

Proof example:

```text
Command: npm test -- auth-policy.test.ts
Exit: 0
Observed: 18 passed, 0 failed, 0 skipped
Meaning: Targeted authorization behavior is verified.
Limitation: Full integration suite was not available locally.
```

## Habit 9: Maintain `Rollback.md`

A rollback plan explains how to return to a known safe state if the change breaks behavior. It is especially required for large or risky edits.

Include:

- Safe branch, tag, release, commit, image, or backup to restore
- Files, packages, configuration, infrastructure, or migrations affected
- Feature flag or kill switch, when one exists
- Data backup and restoration requirements
- Ordered rollback commands or actions
- Owner or approval needed to execute rollback
- Post-rollback checks
- Irreversible effects and data-loss risk

Recommended structure:

```markdown
# Rollback

## Trigger Conditions
- Cross-tenant result observed
- Error rate exceeds the approved threshold

## Last Known Safe State
- Commit: abc1234
- Database schema: migration 202608130945

## Rollback Steps
1. Disable `analytics_chat_v2`.
2. Revert commit def5678.
3. Deploy the previous application image.
4. Reverse the migration only after confirming no new-format rows were written.

## Post-Rollback Verification
- Authentication succeeds.
- Organiser queries return only authorized events.
- Error and latency metrics return to baseline.

## Irreversible or Manual Work
- Rows written after the migration may require a reconciliation script.
```

Rules:

- Do not call a change reversible unless every stateful effect has been considered.
- A git revert is not a complete rollback for data, infrastructure, credentials, queues, caches, or external side effects.
- Update the plan when the actual diff introduces new files, dependencies, migrations, or state changes.
- Test or dry-run rollback steps when the environment and risk permit it.

## Stop Conditions

Stop before making or approving the change when:

- The affected architecture or trust boundary is unknown.
- The request crosses an explicit constraint without authorization.
- The change is destructive or stateful and no credible recovery path exists.
- Required test commands cannot be identified.
- The agent is asked to claim success without running the agreed checks.
- Secrets, production data, access controls, or tenant boundaries may be exposed or bypassed.

## Rationalizations to Reject

| Rationalization | Reality |
|---|---|
| “The user said to do it, so repository constraints no longer matter.” | A task request is not automatic authorization to violate established boundaries. |
| “The architecture is obvious.” | Unverified assumptions create cross-module and security failures. Map or confirm the affected shape. |
| “It is a small change, so tests are unnecessary.” | Size does not prove safety; run the relevant checks. |
| “The tests probably pass.” | Probability is not evidence. Record actual execution or `NOT RUN`. |
| “Git can always undo it.” | Git cannot automatically reverse data loss, external side effects, secrets, or infrastructure state. |
| “Rollback can be figured out later.” | Recovery designed after failure is slower, riskier, and often incomplete. |

## Required Agent Output

Before implementation, provide:

- Affected architecture and boundaries
- Applicable constraints
- Planned checks and expected outcomes
- Rollback requirement and plan
- Unknowns requiring confirmation

After implementation, provide:

- Actual files and components changed
- Constraint compliance review
- Every check run with actual result
- Checks not run and why
- Updated rollback steps
- Remaining risk

## Completion Gate

Do not declare the work safely complete until:

- The affected system shape and trust boundaries are understood or explicitly marked unknown.
- Every applicable constraint is satisfied or an authorized exception is documented.
- Test evidence contains actual commands or actions and observed results.
- Unrun or failed checks remain visible.
- Risky changes have a credible, updated rollback path.
- The final diff introduces no unreviewed dependency, migration, secret, permission change, or irreversible effect.

**Related skills:** Use `ai-review` for plan-first reasoning, one-change scope, and line-by-line diff review; use `ai-context` to preserve the architecture-linked execution and decision trail.
