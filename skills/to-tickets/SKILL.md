---
name: to-tickets
description: Split a plan, spec, or conversation into self-contained tracer-bullet tickets under .issues/<slug>/.
argument-hint: "[path to plan/spec, or issue name]"
disable-model-invocation: true
---

Split the work into **tickets**: tracer-bullet vertical slices, each a standalone markdown file that a fresh session can pick up with zero chat context, each declaring the tickets that **block** it.

An **issue** is one unit of work: a feature, a bug, a diagnosis. Everything for it lives under `.issues/<slug>/`: the spec when there is one, the ticket index, and the tickets.

Arguments: $ARGUMENTS

## Process

1. **Gather.** Work from the conversation. If `/to-spec` wrote `.issues/<slug>/SPEC.md` this session, reuse its slug; the spec is the source and is already in context. If the argument is another file path, read it in full; it is the source. If the argument is a bare name, use it as the slug; otherwise derive a kebab-case slug and confirm it with the user. The index points at the source, and its Decisions hold only the settled implementation decisions; the problem and the stories stay in the source.
2. **Explore the codebase** if you have not already. Use the project's existing names and conventions. Look for prefactoring that would make the change easy.
3. **Draft slices.** Each ticket cuts a narrow but complete path through every layer (schema, API, UI, tests), is demoable on its own, fits one session and one reviewable diff, and carries the background it needs. Prefactoring goes first. Give each ticket its blockers. The one exception is a wide mechanical refactor (a rename across the codebase): sequence it as expand, migrate in batches, contract. A shape that more than one ticket consumes (schema, type, state machine) is a contract: it lives once in the index, and tickets point at it. A large issue groups its tickets by milestone, each milestone demoable on its own.
4. **Confirm.** Present a numbered list (title, blocked by, what it delivers) and ask whether the granularity and blockers are right. Iterate until the user approves.
5. **Write** to `.issues/<slug>/` using the templates below. Name things by responsibility, not file path; paths go stale. Inline a snippet only when it encodes a decision more precisely than prose and only one ticket consumes it. Then tell the user:

> Open a new session and say: solve ticket `.issues/<slug>/01-<slug>.md`. When it finishes it marks the ticket done in the index and records what it delivered. Pick the next ticket whose blockers are all done.

## Templates

`README.md`, the index:

```markdown
# <Issue title>

One line describing the issue.

**Spec:** [SPEC.md](SPEC.md) when it sits beside this file, else the path to the plan this was split from, else None

## Decisions

- <every settled implementation decision, one per line>

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

Read the index first: Decisions, Contracts, the sibling tickets that bound this one, and the spec it links when present. This section carries only what is specific to this ticket, as few sentences as a fresh session needs.

## What to build

The end-to-end behaviour this ticket makes work, from the user's perspective.

## Acceptance criteria

- [ ] Checkable criterion, with how to prove it (a command, a test, a thing to click)

When finished: set Status to `done` here, tick this ticket's row in the index, and add a `**Delivered:**` line under Status naming what later tickets consume (the names you introduced, the gotchas you found).
```
