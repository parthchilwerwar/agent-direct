# agent-direct

Four portable Agent Skills for keeping AI-assisted coding understandable, scoped, verifiable, and resumable.

AI agents can move fast. This repo keeps the human in control of **context, boundaries, review, and handoff** — instead of blindly approving whatever the agent did.

## Why skills, not one giant system prompt

A single mega-prompt grows unreadable and hard to maintain. Skills are small, portable folders — you load only what a task needs, and each concern (context, guardrails, review, control) can be inspected, versioned, and updated on its own.

## Skills

| Skill | Use it for |
|---|---|
| [`ai-context`](skills/ai-context/SKILL.md) | Handover state, decisions, inline intent, execution flow, and bug/feature traces |
| [`ai-guardrails`](skills/ai-guardrails/SKILL.md) | Architecture, constraints, verification evidence, and rollback planning |
| [`ai-review`](skills/ai-review/SKILL.md) | Plan-first reasoning, one logical change at a time, and full diff review |
| [`ai-control`](skills/ai-control/SKILL.md) | Session handoffs backed by an append-only JSONL approval log, model/context provenance, and the human mental-model gate |

Together they operationalize 15 habits for safer AI-assisted coding — without locking you into one model or one coding agent.

## Install

### Skills CLI

```bash
npx skills add parthchilwerwar/agent-direct --all
```

Preview before installing:

```bash
npx skills add parthchilwerwar/agent-direct --list
```

The CLI discovers `ai-context`, `ai-guardrails`, `ai-review`, and `ai-control` directly from this repo.

### Manual install

Clone or download this repo, then copy the skill folders you want from [`skills/`](skills/) into the location your agent scans. Keep each folder intact — every skill needs its own `SKILL.md`.

| Agent | Project / repo | Personal / global |
|---|---|---|
| Codex CLI / IDE | `<repo>/.agents/skills/` | `~/.agents/skills/` |
| Claude Code | `<repo>/.claude/skills/` | `~/.claude/skills/` |
| Other Agent Skills-compatible tools | Use `.agents/skills/` when supported | Check that agent's own skill-discovery docs |

Installing all four should produce:

```text
<skills-directory>/
├── ai-context/
│   ├── SKILL.md
│   └── CHANGELOG.md
├── ai-guardrails/
│   ├── SKILL.md
│   └── CHANGELOG.md
├── ai-review/
│   ├── SKILL.md
│   └── CHANGELOG.md
└── ai-control/
    ├── SKILL.md
    └── CHANGELOG.md
```

Restart or refresh your agent if the skills don't show up right away.


## Repository layout

```text
agent-direct/
├── README.md
└── skills/
    ├── ai-context/
    │   ├── SKILL.md
    │   └── CHANGELOG.md
    ├── ai-guardrails/
    │   ├── SKILL.md
    │   └── CHANGELOG.md
    ├── ai-review/
    │   ├── SKILL.md
    │   └── CHANGELOG.md
    └── ai-control/
        ├── SKILL.md
        └── CHANGELOG.md
```
