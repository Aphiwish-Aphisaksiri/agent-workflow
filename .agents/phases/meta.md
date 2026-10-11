# Phase — Meta

**Trigger:** `Meta: [goal]`

## Objective
Improve the agent workflow system itself — the phases, templates, context files, docs, and tooling under `.agents/` and `AGENTS.md`.

## Scope
- Updating phase instructions in `.agents/phases/`
- Adding, renaming, or restructuring `.agents/context/` files
- Adding reference documents to `.agents/docs/`
- Creating reusable templates or prompt patterns
- Proposing new phases, sub-phases, or workflow conventions

## Steps
1. Understand the current state of the relevant workflow files.
2. Identify what improvement is being requested and why it is needed.
3. Draft the proposed changes and explain the rationale.
4. For changes to core workflow files (`AGENTS.md`, phase files, context templates), confirm with the user before applying.
5. Apply the changes and update `.agents/feature/` with a new or updated meta plan if the change is significant.

## Rules
- Meta is not a catch-all for general tasks — it targets the workflow system only.
- Changes to `AGENTS.md` or phase files that affect all future phases should be treated with care; explain trade-offs before applying.
