---
name: to-tickets
description: Split a plan or conversation into self-contained tracer-bullet tickets under .issues/<slug>/, one per fresh session. Use when the work is too big for one session.
argument-hint: "[path to plan, or issue name]"
disable-model-invocation: true
---

Split the work into **tickets**: tracer-bullet vertical slices, each a standalone markdown file that a fresh session can pick up with zero chat context, each declaring the tickets that **block** it.

An **issue** is one unit of work: a feature, a bug, a diagnosis. Everything for it lives under `.issues/<slug>/`: the ticket index and the tickets.

Arguments: $ARGUMENTS

## Process

1. **Gather.** Find the source. If the argument is a file path, read it in full; it is the source. Else the conversation is the source. A bare-name argument is the slug, otherwise derive a kebab-case slug and confirm it with the user.
2. **Explore the codebase** until every module a ticket names is a real name in the project. Use the project's existing names and conventions. Look for prefactoring that would make the change easy.
3. **Draft slices.** Each ticket is a tracer bullet with its blockers:
   - A narrow but complete path through every layer (schema, API, UI, tests), demoable on its own, fitting one session and one reviewable diff, carrying the background it needs.
   - Prefactoring goes first.
   - The exception to vertical slicing is a wide mechanical refactor (a rename across the codebase): sequence it as expand, migrate in batches, contract.
   - A shape that more than one ticket consumes (schema, type, state machine) is a **contract**: it lives once in the index, and tickets point at it.
   - A large issue groups its tickets by milestone, each milestone demoable on its own.
4. **Confirm.** Present a numbered list (title, blocked by, what it delivers) and ask whether the granularity and blockers are right. Iterate until the user approves.
5. **Write** to `.issues/<slug>/` using the templates below. Name things by responsibility, not file path; paths go stale. Inline a snippet only when it encodes a decision more precisely than prose and only one ticket consumes it. Then tell the user:

> Open a new session and say: solve ticket `.issues/<slug>/01-<slug>.md`. When it finishes it marks the ticket done in the index and records what it delivered. Pick the next ticket whose blockers are all done.

## Templates

`README.md`, the index:

```markdown
# <Issue title>

<One line describing the issue.>

**Source:** <path to the plan this was split from, or None>

## Problem

<The problem and the intended outcome from the user's perspective, a few sentences.>

## Decisions

- <Every settled implementation decision, one per line.>

## Contracts

<shapes more than one ticket consumes: schema, type, state machine; omit the section if none>

| # | Ticket | Blocked by | Done |
|---|--------|------------|------|
| 01 | [<title>](01-<slug>.md) | None | [ ] |
| 02 | [<title>](02-<slug>.md) | 01 | [ ] |

For a large issue, one table per milestone, under a `## Milestone: <name>` heading.
```

`<NN>-<slug>.md`, one per ticket, numbered from 01 in dependency order:

```markdown
# <NN>: <Ticket title>

**Blocked by:** <NN: title>, or None
**Status:** todo

## Context

Read the index first: Problem, Decisions, Contracts, the tickets that block this one and the ones it blocks.

<Only what is specific to this ticket, as few sentences as a fresh session needs.>

## What to build

<The end-to-end behaviour this ticket makes work, from the user's perspective.>

## Acceptance criteria

- [ ] <Checkable criterion, with how to prove it: a command, a test, a thing to click>

When finished: set Status to `done` here, tick this ticket's row in the index, and add a `**Delivered:**` line under Status naming what later tickets consume (the names you introduced, the gotchas you found).
```
