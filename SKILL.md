---
name: software-review-team
description: Run a 21-member enterprise software engineering team deliberation (10 senior engineers on Fable 5.1, 10 junior engineers on Opus 5.5, plus an isolated Outside Observer on Fable 5.1) on an architecture, design, migration, security, reliability, or delivery decision. Use when the user wants an engineering team's verdict, an enterprise design review, or says "software review team", "eng team", "engineering council", or "run the team on this". Deliberation only — the team does not write or change code.
---

# Software Review Team

A structured deliberation by a full enterprise engineering team. The team
advises; it never edits files, commits, or runs mutating commands.

If `$ARGUMENTS` is non-empty, treat it as the problem statement. Otherwise ask
the user for the decision to deliberate on.

## Read First

- `references/protocol.md` — the rounds, packets, and rules. Follow it exactly.
- `references/roster.yaml` — the 21 members, their level, and their model.
- `references/verdict-template.md` — the final output shape.
- `roles/<id>.md` — read each role file only when building that member's packet.

## Flags

- `--members a,b,c` — run only these member ids (Principal Eng and the
  Outside Observer are always added).
- `--show-rounds` — append condensed round summaries after the verdict.

With no flags, all 20 team members deliberate and the Outside Observer reviews.

## Models

Every member is a separate subagent spawned with the Agent tool
(`subagent_type: "general-purpose"`) and the `model` from `roster.yaml`:

- `level: senior` and `level: observer` → `model: "fable"` (Fable 5.1)
- `level: junior` → `model: "opus"` (Opus 5.5)

Never simulate members in the main session. If subagents cannot be spawned,
stop and tell the user; do not fall back to a single-agent simulation.

## Guardrails

- Members are read-only: they may read files, search, and run read-only shell
  commands (`git log`, `git diff`, `ls`, test listing). No edits, no network
  writes, no spawning further agents.
- Do not force consensus. Unresolved disagreement is reported, not hidden.
- The Outside Observer never sees round transcripts — only the problem, the
  context brief, the repo, and the Principal Eng's verdict.
- Spawn every agent within a round in a single message so they run in parallel.
