# to-tickets

Split a plan or conversation into self-contained tracer-bullet tickets under .issues/<slug>/, one per fresh session. Use when the work is too big for one session.

## Source

Adapted from [mattpocock/skills](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-tickets/SKILL.md). Tracker integration, setup dependencies, and the spec step (a former to-spec skill in this repo) removed; tickets are written as local files only.

- Author: Matt Pocock (original), Yilin Fang (adaptation)
- License: MIT

## Typical workflow

1. Talk or grill with the agent until a plan or a decision for a feature or task is settled.
2. If the work does not fit one session and one reviewable diff, run the skill. Give it either a plan file or an issue name; with no plan file, the conversation is the source:

   ```
   /to-tickets docs/plans/dark-mode.md
   /to-tickets dark-mode
   ```

   The skill writes an index at `.issues/dark-mode/README.md` and one ticket file per row, for example `.issues/dark-mode/01-theme-tokens.md`.

3. Open a new session and say: solve ticket `.issues/dark-mode/01-theme-tokens.md`. The session marks the ticket done in the index and records what it delivered.
4. Open the index, pick the first row that is not ticked and whose "Blocked by" tickets are all ticked, and repeat step 3.

The exact wording of the ticket workflow lives in [SKILL.md](SKILL.md); this README only summarises it.

## Decisions

Settled design choices, one per line. Reversing one needs the user's approval.

- Tickets are local markdown files under `.issues/<slug>/`. Tracker integration and its setup dependencies were removed so the skill works in any repo with zero configuration.
- There is no spec step. The first session discusses until a decision exists and implements it when small; when too big, this skill splits it. A former `to-spec` skill was dropped because a spec without tickets never happened in practice.
- The source is a plan file when the argument is a path, otherwise the conversation. Both paths fill the same index.
- The index carries Problem, Decisions, and Contracts so a fresh session reads settled decisions once instead of re-deciding them. Shapes consumed by more than one ticket live only in Contracts; tickets point at them.
- Every ticket is a tracer bullet: a narrow path through every layer, demoable alone, one session, one reviewable diff. The one exception is a wide mechanical refactor, sequenced as expand, migrate, contract.
- A finished ticket writes a `Delivered:` line naming what later tickets consume, and a `Found:` line per out-of-scope finding. The ticket keeps its scope; the user decides whether a finding becomes a ticket.
- Tickets name things by responsibility, not file path, because paths go stale.
