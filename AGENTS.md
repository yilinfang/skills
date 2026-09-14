# skills

Users install these skills with `npx skills add yilinfang/skills`. Every file under `skills/<name>/` ships to every installer, so a skill folder contains only what an agent needs: `SKILL.md`, its reference files, product metadata under `agents/`, and a `README.md`. Maintainer guidance lives here and in each skill's README.

## Editing a skill

1. Load the `writing-for-agents` skill before you edit a `SKILL.md`.
2. Read the skill's `README.md` first. Its Decisions section records why the skill has its current design. Get the user's approval before you reverse a decision.
3. `SKILL.md` is the source of truth. After you edit it, update the README summary so the two agree.
4. Record a settled design change as one line in the README's Decisions section.
5. Keep product-specific interface and invocation policy in `agents/`, and keep the instructions in `SKILL.md` portable when possible.
