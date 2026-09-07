---
name: to-tickets
description: Split a plan, spec, or the current conversation into self-contained tracer-bullet tickets under .tickets/<feature-slug>/, one file per ticket, each declaring what blocks it, so fresh sessions can implement them one at a time.
argument-hint: "[path to plan/spec, or feature name]"
disable-model-invocation: true
---

# To Tickets

Split a plan, spec, or conversation into a set of **tickets**: tracer-bullet vertical slices, each declaring the tickets that **block** it. Every ticket is a standalone markdown file that a brand-new session can pick up with no other context.

Arguments: $ARGUMENTS

## Process

### 1. Gather context

Work from whatever is already in the conversation. If the argument is a file path (plan, spec, design doc), read it in full. If the argument is a bare name, use it as the feature slug. Otherwise derive a short kebab-case slug from the feature and confirm it with the user before writing anything.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code. Use the project's existing names for things, and respect any conventions documented in CLAUDE.md, AGENTS.md, or CONTRIBUTING files if present.

Look for opportunities to prefactor the code to make the implementation easier. "Make the change easy, then make the easy change."

### 3. Draft vertical slices

Break the work into **tracer bullet** tickets.

<vertical-slice-rules>

- Each slice cuts a narrow but COMPLETE path through every layer (schema, API, UI, tests): vertical, NOT a horizontal slice of one layer
- A completed slice is demoable or verifiable on its own
- Each slice is sized to fit in a single fresh context window
- Each slice must be readable with ZERO conversation context: the implementer is a brand-new session that has never seen this chat, so the ticket carries the background it needs rather than pointing back here
- Any prefactoring should be done first

</vertical-slice-rules>

Give each ticket its **blocking edges**: the other tickets that must complete before it can start. A ticket with no blockers can start immediately.

**Wide refactors are the exception to vertical slicing.** A **wide refactor** is one mechanical change (rename a column, retype a shared symbol) whose **blast radius** fans across the whole codebase, so a single edit breaks thousands of call sites at once and no vertical slice can land green. Don't force it into a tracer bullet; sequence it as **expand–contract**. First expand: add the new form beside the old so nothing breaks. Then migrate the call sites over in batches sized by blast radius (per package, per directory), each batch its own ticket blocked by the expand, keeping CI green batch to batch because the old form still exists. Finally contract: delete the old form once no caller remains, in a ticket blocked by every migrate batch. When even the batches can't stay green alone, keep the sequence but let them share an integration branch that all block a final integrate-and-verify ticket; green is promised only there.

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each ticket, show:

- **Title**: short descriptive name
- **Blocked by**: which other tickets (if any) must complete first
- **What it delivers**: the end-to-end behaviour this ticket makes work

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the blocking edges correct: does each ticket only depend on tickets that genuinely gate it?
- Should any tickets be merged or split further?

Iterate until the user approves the breakdown.

### 5. Write the ticket files

Write the approved tickets under `.tickets/<feature-slug>/` in the current workspace:

- `.tickets/<feature-slug>/README.md`: the index. One line summarising the feature, then a table with one row per ticket: number, title, blocked by, status checkbox. The user reads this to choose the next ticket.
- `.tickets/<feature-slug>/<NN>-<slug>.md`: one file per ticket, numbered from `01` in dependency order (blockers first). Use the ticket template below. One ticket per file, never a single combined file.

When done, tell the user how to use them:

> Open a new session and say: "solve ticket `.tickets/<feature-slug>/01-<slug>.md`". When that session finishes, it marks the ticket done in the index. Then pick the next ticket whose blockers are all done and start another session.

<index-template>

# <Feature title>

One line describing the feature.

| # | Ticket | Blocked by | Done |
|---|--------|------------|------|
| 01 | [<title>](01-<slug>.md) | None | [ ] |
| 02 | [<title>](02-<slug>.md) | 01 | [ ] |

</index-template>

<ticket-template>

# <NN>: <Ticket title>

**Feature:** <feature-slug> (index: `.tickets/<feature-slug>/README.md`)
**Blocked by:** <NN: title>, ..., or "None (can start immediately)"
**Status:** todo

## Context

Two to five sentences a fresh session needs: what the feature is, which decisions were already made, and what the blocking tickets have put in place.

## What to build

The end-to-end behaviour this ticket makes work, from the user's perspective, not a layer-by-layer implementation list.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2

## Verification

How the implementer proves it works: a command to run, a test to add, a thing to click.

## Done

When finished: set **Status** to `done` in this file and tick this ticket's row in the index README.

</ticket-template>

Avoid specific file paths or code snippets in tickets: they go stale fast. Exception: a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape). Inline it, trimmed to the decision-rich parts.
