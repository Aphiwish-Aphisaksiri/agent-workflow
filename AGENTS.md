# Agent Workflow

This file defines the phase-driven workflow for AI agents on this project.
Detect the active phase from the **first word(s) of the user's prompt** and follow the corresponding instructions precisely.

> **Adaptation note:** This file may be renamed to `.cursorrules`, `.github/copilot-instructions.md`, or the equivalent for your agent platform. The content and conventions remain the same.

---

## Phase Detection

| Prompt prefix | Phase | Purpose |
|---|---|---|
| `Plan: [goal]` | **Plan** | Translate a goal into a structured feature plan |
| `Refine: [feature or plan file]` | **Refine** | Stress-test and strengthen an existing plan |
| `Implement: [feature or plan file]` | **Implement** | Execute an approved plan — no new decisions |
| `Fix: [what to fix]` | **Fix** | Apply a targeted fix — visual, behavioral, or code quality |
| `Meta: [goal]` | **Meta** | Improve this workflow system itself |

If no prefix is detected, ask the user which phase applies before proceeding.

---

## Context Files

**Every interaction, read `.agents/context/ai-workflow-rules.md` first.** This file contains project-specific rules that may override default phase behavior.

Then, before starting any phase, read the relevant files below:

| File | Contents |
|---|---|
| `.agents/context/project-context.md` | Project name, description, users, key workflows, setup |
| `.agents/context/architecture.md` | Tech stack, project structure, page architecture, data flow |
| `.agents/context/code-standards.md` | File organization, naming conventions, coding patterns, testing |
| `.agents/context/ui-context.md` | Component library, theme, color scheme, layout and behavior patterns |
| `.agents/context/progress-tracker.md` | Current phase, feature checklist, project decisions, blockers |

Read all files relevant to the current task. Do not assume their current stage.

---

## Reference Documents

`.agents/docs/` contains reference materials such as API specs, research notes, and design documents.
Consult `.agents/docs/README.md` to find what is available, and read specific docs when a plan references external knowledge.

---

## Feature Plans

All feature plans live in `.agents/feature/` using this naming convention:

```
NNN-[feature-name].md        → single-file plan          e.g. 001-auth.md
NNNa-[feature-name].md       → split plan, part A        e.g. 002a-data-model.md
NNNb-[feature-name].md       → split plan, part B        e.g. 002b-api-layer.md
```

Use the next available three-digit number. Do not reuse or skip numbers.

> **Reference example:** `.agents/feature/NNN-example.md` contains a fully annotated example plan with every field filled in. It is required reading for the Plan and Refine phases.

---

## Phase 1 — Plan

**Trigger:** `Plan: [goal]`

### Objective
Translate a high-level goal into a complete, unambiguous plan document that can be handed to Implement without further design decisions.

### Steps
1. Read `.agents/feature/NNN-example.md` for the plan structure and conventions.
2. Analyze the goal and identify missing information, ambiguous requirements, and decisions that must be made before a solid plan can be written.
3. **If gaps exist:** Consolidate all questions into a single response and ask the user. Wait for answers before writing the plan. Ask follow-ups only if the user's answers introduce new gaps — not as routine rounds.
4. Write the plan to `.agents/feature/NNN-[feature-name].md` following the structure defined in `NNN-example.md`.
5. Summarize the plan and list any items left as open questions.

### Rules
- Do not write any code or make file changes during this phase.
- Never leave a decision implicit — if you make a call, record it under **Decisions Made**.
- A plan is ready for Implement only when **Open Questions** is empty.

---

## Phase 2 — Refine

**Trigger:** `Refine: [feature name, plan file, or description]`

### Objective
Find weaknesses, gaps, and hidden assumptions in an existing plan before it reaches implementation.

### Steps
1. Read `.agents/feature/NNN-example.md` to confirm expected plan structure and conventions.
2. Read the target plan from `.agents/feature/`.
3. Systematically evaluate the plan:
   - Are all steps concrete and actionable?
   - Are there missing edge cases, error conditions, or rollback needs?
   - Do any steps contradict each other or the context files?
   - Are all external dependencies identified?
   - Would a developer be able to implement every step without ambiguity?
4. **Present your analysis to the user.** Include:
   - Weaknesses and gaps found
   - Open questions that need user input
   - Suggested changes (with rationale)
5. **STOP. Do not edit any files until the user responds.**
6. After receiving user answers, update the plan document with resolved decisions, revised steps, and any structural changes. Add a refinement round entry at the top of the plan documenting concerns raised and how they were resolved.
7. Remove resolved items from **Open Questions**. The plan is Refine-complete when **Open Questions** is empty.

### Rules
- Do not implement anything during this phase.
- Be specific about weaknesses — "step 3 is ambiguous because X" not "step 3 needs more detail".
- If the plan requires major restructuring, describe the proposed changes and confirm with the user before rewriting.

---

## Phase 3 — Implement

**Trigger:** `Implement: [feature name, plan file, or description]`

### Objective
Execute the approved plan faithfully. No design decisions, no scope additions.

### Steps
1. Read the target plan from `.agents/feature/`. Verify **Open Questions** is empty. If not, stop and ask the user to run a Refine pass first.
2. Execute all implementation steps in the plan in order.
3. Run the **Verification** steps from the plan — build, lint, type-check, tests, integration / smoke-test, and regression as applicable. If any check fails, fix the issue before proceeding.
4. Update the feature plan file — Check off all completed steps (`- [x]`). Only do this after all verification passes.
5. Report a summary of what was done.
6. Update `.agents/context/progress-tracker.md` — check off the completed feature, update **Current Phase**, **Next Steps**, and clear any resolved **Blockers**.

### Rules
- **Do not make architectural or design decisions.** If a step is ambiguous during execution, **stop** — present concrete interpretations (Option A, B, and/or C), give your recommendation with reasoning, and wait for the user to decide before continuing. If a real decision is required and cannot be safely inferred, report what is blocked and ask the user to run a Refine pass.
- Do not add features, improvements, or refactors that are not in the plan.
- Do not bypass safety checks or destructive operations without explicit confirmation in the plan.
- Check off steps only after all verification passes, not during implementation.

---

## Phase 4 — Fix

**Trigger:** `Fix: [what to fix]`

This phase can be used in two ways:
- **Standalone** — Start a new session with `Fix:` to apply a targeted fix without a full Plan → Refine → Implement cycle.
- **Escalated from Implement** — If an Implement step surfaces a small defect that must be resolved to continue, the agent may enter Fix mode for that specific issue, then return to Implement.

### Objective
Apply a narrow, well-scoped fix to an existing issue. No architectural decisions, no new features, no refactoring beyond what is required to resolve the stated problem.

### Steps
1. Read the affected files to understand the current state.
2. Identify the exact root cause of the issue described.
3. Apply the fix, scoped strictly to what was described.
4. Report what was changed and why.

### Rules
- No refactoring beyond the fix.
- **No design or architectural decisions.** If the fix requires a design decision or touches more than the stated scope, **stop** — present Option A and Option B with a recommendation and ask the user whether to proceed or escalate to a Plan.
- Do not update `progress-tracker.md` for fixes unless the fix resolves a tracked blocker.

---

## Phase 5 — Meta

**Trigger:** `Meta: [goal]`

### Objective
Improve the agent workflow system itself — the phases, templates, context files, docs, and tooling under `.agents/` and `AGENTS.md`.

### Scope
- Updating phase instructions in `AGENTS.md`
- Adding, renaming, or restructuring `.agents/context/` files
- Adding reference documents to `.agents/docs/`
- Creating reusable templates or prompt patterns
- Proposing new phases, sub-phases, or workflow conventions

### Steps
1. Understand the current state of the relevant workflow files.
2. Identify what improvement is being requested and why it is needed.
3. Draft the proposed changes and explain the rationale.
4. For changes to core workflow files (`AGENTS.md`, context templates), confirm with the user before applying.
5. Apply the changes and update `.agents/feature/` with a new or updated meta plan if the change is significant.

### Rules
- Meta is not a catch-all for general tasks — it targets the workflow system only.
- Changes to `AGENTS.md` that affect all future phases should be treated with care; explain trade-offs before applying.