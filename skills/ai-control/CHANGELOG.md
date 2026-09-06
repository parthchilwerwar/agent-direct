# Changelog

## 2026-09-06 — Structured resume and approval history

- Added schema v1 for per-session, append-only JSONL tool calls, approval evidence, constraint snapshots, and observed diffs.
- Resume now validates and reduces the artifact before using the skim-only handoff; legacy prose-only handoffs continue with a warning and independently checked authorization.
- Documented independent checkpoints, storage enforcement limits, interrupted sessions, and secret redaction.
