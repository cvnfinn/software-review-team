# software-review-team

A Claude Code skill that runs a 21-member enterprise engineering team
deliberation on a design, architecture, migration, security, or delivery
decision. Deliberation only — the team never edits code.

- **10 senior engineers** — Fable 5.1 (`model: fable`)
- **10 junior engineers** — Opus 5.5 (`model: opus`)
- **1 Outside Observer** — Fable 5.1, isolated from the debate, audits the
  Principal Engineer's verdict and can veto once

## Install

```bash
git clone https://github.com/cvnfinn/software-review-team ~/.claude/skills/software-review-team
```

## Use

```
/software-review-team Should we split the billing module out of the monolith?
/software-review-team --members staff-backend,data-architect,senior-sre,backend-eng Move sessions to Redis?
/software-review-team --show-rounds <problem>
```

## Protocol

1. **Context brief** — main session summarizes the repo and diff once.
2. **Round 1** — all 20 members analyse independently, in parallel.
3. **Round 2** — each member cross-examines all peers' round 1 output, in parallel.
4. **Round 3** — Principal names the top 3 disagreements; only involved members give final positions.
5. **Synthesis** — Principal Engineer writes the verdict (ADR-style, with risk register and minority report).
6. **Observer review** — Outside Observer approves or vetoes; one revision loop, then dissent is shown.

A full run is roughly 45+ agent calls, 30 of them on Fable. Use `--members` for smaller questions.

## Roster

| Senior (Fable 5.1) | Junior (Opus 5.5) |
|---|---|
| Principal Engineer | Backend Engineer |
| Staff Backend Engineer | Frontend Engineer |
| Staff Frontend Engineer | Full-stack Engineer |
| Senior SRE | DevOps Engineer |
| Security Lead | QA / Test Engineer |
| Data / Database Architect | Data Engineer |
| QA Lead | Release / Build Engineer |
| Engineering Manager | Security Analyst |
| Compliance & Governance Lead | Performance Engineer |
| Senior ML / AI Engineer | Technical Writer / DX |

Plus: **Outside Observer** (Fable 5.1).

Role definitions: [`roles/`](roles). Protocol: [`references/protocol.md`](references/protocol.md).
