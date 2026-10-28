# Software Review Team Protocol

Main session = orchestrator. It gathers context, spawns members, passes
outputs between rounds, and presents the result. It does not add its own
opinions to the deliberation.

## Round 0: Context Brief (main session)

Build one brief so 20 agents don't each re-explore from scratch:

- If inside a git repo: stack and languages, top-level layout, the files most
  relevant to the problem, `git status`, and `git diff` (staged + unstaged,
  truncated to the relevant hunks), recent `git log --oneline -15`.
- Existing constraints visible in the repo: CI config, infra/IaC, schema
  and migration files, auth, observability setup, test setup.
- If not in a repo: state "No codebase — reason from the problem statement."

Keep the brief under ~1,500 words. Members may read further files themselves.

## Round 1: Independent Analysis (20 parallel agents)

All team members except the Outside Observer, spawned in one message.
Each sees only: the role file, the problem, the context brief. Blind to peers.

Packet:

```text
You are one member of an enterprise software engineering team deliberating on
a decision. You are read-only: do not edit files, commit, or spawn agents.

Your role:
{contents of roles/<id>.md}

Problem:
{problem}

Context brief:
{brief}

Read any further code you need. Then produce, in at most 400 words:
1. Your analysis through your role's lens (use your Output section).
2. Concrete risks you see, each with severity (critical/high/medium/low).
3. Your verdict, confidence (0-100%), and where you may be wrong.
Cite file paths where relevant. Do not anticipate other members.
```

## Round 2: Cross-Examination (20 parallel agents)

Each member receives all 19 peers' round 1 outputs, labelled by role.

Packet:

```text
{role file}
Problem: {problem}

Your round 1 analysis:
{own output}

Your teammates' round 1 analyses:
{peer outputs, each headed "## <Role title> (<level>)"}

In at most 300 words:
1. The position you most disagree with, by role, and why.
2. The insight from a teammate that most changed or sharpened your view.
3. Any risk a teammate missed that your role owns.
4. Your updated verdict and confidence.
Engage at least three teammates by role.
```

Juniors are expected to challenge seniors where they have ground-level
evidence; seniors must answer the substance, not the rank.

## Round 3: Targeted Resolution (Principal Eng + involved members)

1. Spawn the Principal Eng with all round 2 outputs. Ask it to name the **three
   largest unresolved disagreements**, and for each, the roles on each side.
2. Spawn only the involved members (parallel), each given the disagreement(s)
   they're in and the opposing arguments. Ask for a final position in at most
   150 words, no new topics.

If round 2 shows no real disagreement, run one counterfactual pass instead:
ask the five members with the highest confidence to argue the strongest case
that the consensus is wrong.

## Synthesis (Principal Eng)

Spawn the Principal Eng with: problem, brief, all round 1–3 outputs, and
`verdict-template.md`. It writes the full verdict. It must keep the minority
report and must not invent agreement.

## Observer Review (Outside Observer)

Spawn the Outside Observer with ONLY: its role file, the problem, the context
brief, repo access, and the Principal's verdict. No round transcripts.

It returns `APPROVE` or `VETO` with findings.

- `APPROVE` → present the verdict with the Observer's notes appended.
- `VETO` → spawn the Principal Eng once more with the verdict and the
  Observer's findings; it revises. Send the revision to the Observer again.
  - Second `APPROVE` → present the revised verdict.
  - Second `VETO` → present the revised verdict with the Observer's dissent
    shown in full directly beneath the Decision section. No third loop.

## Presentation (main session)

Show the final verdict as written. With `--show-rounds`, append one
condensed line per member per round. Report the agent count and note any
member that failed or timed out.

## Failure Handling

- A member that errors or times out is retried once; if it fails again, the
  run continues and the gap is listed under Protocol Notes in the verdict.
- If the Principal Eng fails, retry once, then stop and report — no verdict
  is presented without a Principal synthesis.
