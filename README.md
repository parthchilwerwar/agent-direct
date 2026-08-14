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

There is no package or universal CLI installer yet. Download or clone this repository, then copy the skill directories you want from [`skills/`](skills/) into the location your agent scans. Keep each directory intact so every skill still contains its own `SKILL.md`.

| Agent | Project or repository | Personal or global |
|---|---|---|
| Codex CLI / IDE | `<repo>/.agents/skills/` | `~/.agents/skills/` |
| Claude Code | `<repo>/.claude/skills/` | `~/.claude/skills/` |
| Other Agent Skills-compatible tools | Use `.agents/skills/` when supported | Check that agent's skill-discovery documentation |

For example, installing all four skills should produce:

```text
<skills-directory>/
├── ai-context/
│   └── SKILL.md
├── ai-guardrails/
│   └── SKILL.md
├── ai-review/
│   └── SKILL.md
└── ai-control/
    └── SKILL.md
```

Restart or refresh your agent if the skills do not appear immediately.

### ChatGPT

Do not copy these files into `.chatgpt/skills/`; OpenAI does not document that as a local skill-discovery path. Standalone skills are available in the ChatGPT desktop app through its **Skills** interface. For ChatGPT on the web or mobile, this repository would need to be distributed as a plugin.

Codex installations may also discover personal skills from `~/.codex/skills/`. Current OpenAI documentation lists `.agents/skills/` as the portable repository and personal location.

See the official [OpenAI skill guide](https://learn.chatgpt.com/docs/build-skills) and [Claude Agent Skills guide](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) for current platform-specific details.

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
