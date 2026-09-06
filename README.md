# agent-direct

Four portable Agent Skills for keeping AI-assisted coding scoped, verifiable, and resumable.

Most agent failures aren't bad code — they're a human who stopped checking the diff. These four skills make scoped and resumable the default, instead of something you have to remember to set up.

## Why skills instead of one big system prompt

A single mega-prompt turns into a wall of text nobody wants to touch. Skills are small, portable folders — an agent only loads the one that matches what you're doing, and each concern (context, guardrails, review, control) can be edited on its own without breaking the rest. The description line does most of the work here: it's the only thing an agent sees before deciding to load a skill, so keeping each one narrow is what stops them from colliding.

## Skills

| Skill | Use it for |
|---|---|
| [`ai-context`](skills/ai-context/SKILL.md) | Handover state, decisions, inline intent, execution flow, and bug/feature traces |
| [`ai-guardrails`](skills/ai-guardrails/SKILL.md) | Architecture, constraints, verification evidence, and rollback planning |
| [`ai-review`](skills/ai-review/SKILL.md) | Plan-first reasoning, one logical change at a time, and full diff review |
| [`ai-control`](skills/ai-control/SKILL.md) | Session handoffs backed by an append-only JSONL approval log, model/context provenance, and the human mental-model gate |

Together they cover 15 habits for safer AI-assisted coding, without tying you to one model or one coding agent.

## Install

### Skills CLI

```bash
npx skills add parthchilwerwar/agent-direct --all
```

Preview before installing:

```bash
npx skills add parthchilwerwar/agent-direct --list
```

The CLI picks up `ai-context`, `ai-guardrails`, `ai-review`, and `ai-control` directly from this repo.

### Manual install

Clone or download the repo, then copy the skill folders you want from [`skills/`](skills/) into wherever your agent scans for skills. Keep each folder intact — every skill needs its own `SKILL.md`.

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
