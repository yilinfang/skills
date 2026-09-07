---
name: to-tickets
description: Split a plan, spec, or conversation into self-contained tracer-bullet tickets under .tickets/<feature-slug>/.
argument-hint: "[path to plan/spec, or feature name]"
disable-model-invocation: true
---

Split the work into **tickets**: tracer-bullet vertical slices, each a standalone markdown file that a fresh session can pick up with zero chat context, each declaring the tickets that **block** it.

Arguments: $ARGUMENTS

## Process

1. **Gather.** Work from the conversation. If the argument is a file path, read it in full; the index then points at it as `Source:` and its Decisions hold only the settled implementation decisions, with the problem and stories left to the source. If it is a bare name, use it as the feature slug; otherwise derive a kebab-case slug and confirm it with the user.
2. **Explore the codebase** if you have not already. Use the project's existing names and conventions. Look for prefactoring that would make the change easy.
3. **Draft slices.** Each ticket cuts a narrow but complete path through every layer (schema, API, UI, tests), is demoable on its own, fits one session and one reviewable diff, and carries the background it needs. Prefactoring goes first. Give each ticket its blockers. The one exception is a wide mechanical refactor (a rename across the codebase): sequence it as expand, migrate in batches, contract. A shape that more than one ticket consumes (schema, type, state machine) is a contract: it lives once in the index, and tickets point at it. A large feature groups its tickets by milestone, each milestone demoable on its own.
4. **Confirm.** Present a numbered list (title, blocked by, what it delivers) and ask whether the granularity and blockers are right. Iterate until the user approves.
5. **Write** to `.tickets/<feature-slug>/` using the templates below. Name things by responsibility, not file path; paths go stale. Inline a snippet only when it encodes a decision more precisely than prose and only one ticket consumes it. Then tell the user:

> Open a new session and say: solve ticket `.tickets/<feature-slug>/01-<slug>.md`. When it finishes it marks the ticket done in the index and records what it delivered. Pick the next ticket whose blockers are all done.

## Templates

`README.md`, the index:

```markdown
# <Feature title>

One line describing the feature.

**Source:** <path to the plan or spec this was split from>, or None

## Decisions

- <every decision already settled, one per line>

## Contracts

<shapes more than one ticket consumes: schema, type, state machine; omit the section if none>

| # | Ticket | Blocked by | Done |
|---|--------|------------|------|
| 01 | [<title>](01-<slug>.md) | None | [ ] |
| 02 | [<title>](02-<slug>.md) | 01 | [ ] |

For a large feature, one table per milestone, under a `## Milestone: <name>` heading.
```

`<NN>-<slug>.md`, one per ticket, numbered from 01 in dependency order:

```markdown
# <NN>: <Ticket title>

**Blocked by:** <NN: title>, or None
**Status:** todo

## Context

Read the index first: Decisions, Contracts, and the sibling tickets that bound this one. This section carries only what is specific to this ticket, as few sentences as a fresh session needs.

## What to build

The end-to-end behaviour this ticket makes work, from the user's perspective.

## Acceptance criteria

- [ ] Checkable criterion, with how to prove it (a command, a test, a thing to click)

When finished: set Status to `done` here, tick this ticket's row in the index, and add a `**Delivered:**` line under Status naming what later tickets consume (the names you introduced, the gotchas you found).
```
