---
name: to-spec
description: Synthesize the current conversation into a spec at .issues/<slug>/SPEC.md. No interview; the discussion is already done.
argument-hint: "[issue name]"
disable-model-invocation: true
---

Turn what has already been discussed into a **spec**: a standalone markdown file that a fresh session can read to understand the problem, the solution, and every decision settled so far. Synthesize; the interview already happened. Where the conversation left something open, record it as open rather than deciding it.

An **issue** is one unit of work: a feature, a bug, a diagnosis. Everything for it lives under `.issues/<slug>/`.

Arguments: $ARGUMENTS

## Process

1. **Gather.** Work from the conversation. If the argument is a bare name, use it as the issue slug; otherwise derive a kebab-case slug and confirm it with the user.
2. **Explore the codebase** if you have not already. Use the project's existing names and conventions.
3. **Write** to `.issues/<slug>/SPEC.md` using the template below. Name things by responsibility, not file path; paths go stale. Inline a snippet only when it encodes a decision more precisely than prose (schema, type shape, state machine). Then tell the user:

> If one session can finish this, implement it now from the spec. Otherwise run `/to-tickets <slug>` to split it.

## Template

```markdown
# <Issue title>

## Problem

The problem, from the user's perspective.

## Solution

The solution, from the user's perspective.

## User stories

A long numbered list. Every actor and every path through the feature, including the failure paths.

1. As an <actor>, I want <feature>, so that <benefit>.

## Implementation decisions

Every decision already settled, one per line: modules built or modified, their interfaces, schema changes, API contracts, architectural choices, specific interactions.

## Testing decisions

What behaviour gets tested and at which boundary. Prior art: the existing tests in the codebase that a new test should resemble.

## Out of scope

What this spec deliberately leaves out.

## Open questions

Anything the conversation raised and did not settle. Empty is fine.
```
