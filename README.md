# agent-direct

Four portable Agent Skills for keeping AI-assisted coding understandable, scoped, verifiable, and resumable.

AI agents can move fast. The point of this repo is to keep the human in control of **context, boundaries, review, and handoff** instead of blindly approving changes.

## Skills

| Skill | Use it for |
|---|---|
| [`ai-context`](skills/ai-context/SKILL.md) | Handover state, decisions, inline intent, execution flow, and bug/feature traces |
| [`ai-guardrails`](skills/ai-guardrails/SKILL.md) | Architecture, constraints, verification evidence, and rollback planning |
| [`ai-review`](skills/ai-review/SKILL.md) | Plan-first reasoning, one logical change at a time, and full diff review |
| [`ai-control`](skills/ai-control/SKILL.md) | Session handoffs, model/context provenance, and the human mental-model gate |

Together they operationalize 15 habits for safer AI-assisted coding without tying the workflow to one model or coding agent.

## Install

Using the `skills` CLI:

```bash
npx skills add parthchilwerwar/agent-direct \
  --skill ai-context \
  --skill ai-guardrails \
  --skill ai-review \
  --skill ai-control
```

Install only one skill:

```bash
npx skills add parthchilwerwar/agent-direct --skill ai-review
```

Or copy any directory under `skills/` into the skills location supported by your agent.

## Repository layout

```text
agent-direct/
├── README.md
└── skills/
    ├── ai-context/
    │   └── SKILL.md
    ├── ai-guardrails/
    │   └── SKILL.md
    ├── ai-review/
    │   └── SKILL.md
    └── ai-control/
        └── SKILL.md
```

Each skill is self-contained and follows the Agent Skills directory convention: one skill directory with a `SKILL.md` containing YAML frontmatter plus the agent instructions.

## Philosophy

**Do not trust an AI-generated change because the agent says it is done. Make the change traceable, scoped, tested, reviewable, reversible, and understandable.**

## Status

Early public release. Feedback and real-world testing across different coding agents are welcome.
