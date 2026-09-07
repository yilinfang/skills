---
name: to-tickets
description: Split a plan, spec, or conversation into self-contained tracer-bullet tickets under .tickets/<feature-slug>/.
argument-hint: "[path to plan/spec, or feature name]"
disable-model-invocation: true
---

Split the work into **tickets**: tracer-bullet vertical slices, each a standalone markdown file that a fresh session can pick up with zero chat context, each declaring the tickets that **block** it.

Arguments: $ARGUMENTS

## Process

1. **Gather.** Work from the conversation. If the argument is a file path, read it in full. If it is a bare name, use it as the feature slug; otherwise derive a kebab-case slug and confirm it with the user.
2. **Explore the codebase** if you have not already. Use the project's existing names and conventions. Look for prefactoring that would make the change easy.
3. **Draft slices.** Each ticket cuts a narrow but complete path through every layer (schema, API, UI, tests), is demoable on its own, fits one fresh context window, and carries the background it needs. Prefactoring goes first. Give each ticket its blockers. The one exception is a wide mechanical refactor (a rename across the codebase): sequence it as expand, migrate in batches, contract.
4. **Confirm.** Present a numbered list (title, blocked by, what it delivers) and ask whether the granularity and blockers are right. Iterate until the user approves.
5. **Write** to `.tickets/<feature-slug>/` using the templates below. Name things by responsibility, not file path; paths go stale. Inline a snippet only when it encodes a decision more precisely than prose (schema, type shape, state machine). Then tell the user:

> Open a new session and say: solve ticket `.tickets/<feature-slug>/01-<slug>.md`. When it finishes it marks the ticket done in the index. Pick the next ticket whose blockers are all done.

## Templates

`README.md`, the index:

```markdown
# <Feature title>

One line describing the feature.

## Decisions

- <every decision already settled, one per line>

| # | Ticket | Blocked by | Done |
|---|--------|------------|------|
| 01 | [<title>](01-<slug>.md) | None | [ ] |
| 02 | [<title>](02-<slug>.md) | 01 | [ ] |
```

`<NN>-<slug>.md`, one per ticket, numbered from 01 in dependency order:

```markdown
# <NN>: <Ticket title>

**Blocked by:** <NN: title>, or None
**Status:** todo

## Context

Two to five sentences a fresh session needs. Read the index's Decisions first; do not restate them here.

## What to build

The end-to-end behaviour this ticket makes work, from the user's perspective.

## Acceptance criteria

- [ ] Checkable criterion, with how to prove it (a command, a test, a thing to click)

When finished: set Status to `done` here and tick this ticket's row in the index.
```
