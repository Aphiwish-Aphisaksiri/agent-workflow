# Agent Workflow

This file defines the phase-driven workflow for AI agents on this project.
Detect the active phase from the user prompt, then follow the corresponding instructions below.

> **Adaptation note:** This file may be renamed to `.cursorrules`, `.github/copilot-instructions.md`, or the equivalent for your agent platform. The content and conventions remain the same.

---

## Phase Detection

| Prompt prefix | Phase | Purpose |
|---|---|---|
| `Plan: [goal]` | **Plan** | Translate a goal into a structured feature plan |
| `Refine: [feature or plan file]` | **Refine** | Stress-test and strengthen an existing plan |
| `Implement: [feature or plan file]` | **Implement** | Execute an approved plan or fix doc — no new decisions |
| `Fix: [what to fix]` | **Fix** | Document and apply a targeted fix — standalone in `.agents/fix/`, or inline in a feature plan when escalated from Implement |
| `Meta: [goal]` | **Meta** | Improve this workflow system itself |

If no phase keyword is detected, infer the phase from context and instructions. When in doubt, ask the user for clarification.

---

## Context Files

**Every interaction, read `.agents/context/ai-workflow-rules.md` first.** This file contains project-specific rules that may override default phase behavior.

Then, read any other context files relevant to the current task. Do not assume their current state.

| File | Contents |
|---|---|
| `.agents/context/project-context.md` | Project name, description, users, key workflows, setup |
| `.agents/context/architecture.md` | Tech stack, project structure, page architecture, data flow |
| `.agents/context/code-standards.md` | File organization, naming conventions, coding patterns, testing |
| `.agents/context/ui-context.md` | Component library, theme, color scheme, layout and behavior patterns |
| `.agents/context/progress-tracker.md` | Current phase, feature checklist, project decisions, blockers — features only, not fixes |

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

## Fix Documents

Fix documents track small, targeted fixes that are often unrelated to a main feature.
They live in `.agents/fix/` and use the same naming convention and structure as feature plans:

```
NNN-[fix-name].md        → single-file fix doc       e.g. 001-login-redirect-loop.md
NNNa-[fix-name].md       → split fix doc, part A
NNNb-[fix-name].md       → split fix doc, part B
```

Use the next available three-digit number within `.agents/fix/`. Numbering is independent from `.agents/feature/`. Do not reuse or skip numbers.

Fix documents follow the same structure as feature plans (see `.agents/feature/NNN-example.md`) and move through the same Plan → Refine → Implement lifecycle. The `Plan`, `Refine`, and `Implement` phases all accept fix files in `.agents/fix/` in addition to feature files in `.agents/feature/`.

**Standalone vs. escalated:**
- **Standalone fix** — A fix requested on its own (via `Fix:` or `Plan:`) is documented as a new fix doc in `.agents/fix/`.
- **Escalated fix** — A defect discovered mid-Implement that must be resolved to continue is documented **inline in the active feature plan** (not as a separate fix doc), then implementation resumes.

Fixes are lightweight: completed fixes do **not** update `.agents/context/progress-tracker.md`.

---

## Phase 1 — Plan

**Trigger:** `Plan: [goal]`

### Objective
Translate a high-level goal into a complete, unambiguous plan document that can be handed to Implement without further design decisions.

### Steps
1. Read `.agents/feature/NNN-example.md` for the plan structure and conventions.
2. Analyze the goal and identify missing information, ambiguous requirements, and decisions that must be made before a solid plan can be written.
3. **If gaps exist:** Consolidate all questions into a single response and ask the user. Wait for answers before writing the plan. Ask follow-ups only if the user's answers introduce new gaps — not as routine rounds.
4. Write the plan following the structure defined in `NNN-example.md`. Use `.agents/feature/NNN-[feature-name].md` for features, or `.agents/fix/NNN-[fix-name].md` for standalone fixes (see **Fix Documents**).
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
2. Read the target plan from `.agents/feature/` (feature plans) or `.agents/fix/` (fix docs).
3. Systematically evaluate the plan.
4. **Present your analysis to the user.** Include:
   - Weaknesses and gaps found with suggested changes
   - Open questions that need user input (Suggestions are appreciated but optional)
5. After receiving user answers, update the plan document with resolved decisions, revised steps, and any structural changes. Add a refinement round entry at the top of the plan documenting concerns raised and how they were resolved.
6. Remove resolved items from **Open Questions**. The plan is Refine-complete when **Open Questions** is empty.

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
1. Read the target plan from `.agents/feature/` (feature plans) or `.agents/fix/` (fix docs). Verify **Open Questions** is empty. If not, stop and ask the user to run a Refine pass first.
2. Execute all implementation steps in the plan in order.
3. Run the **Verification** steps from the plan — build, lint, type-check, tests, integration / smoke-test, and regression as applicable. If any check fails, fix the issue before proceeding.
4. Update the plan file — Check off all completed steps (`- [x]`). Only do this after all verification passes.
5. Report a summary of what was done.
6. Update `.agents/context/progress-tracker.md`. **Exception:** skip this step when implementing a fix doc from `.agents/fix/` — fixes are lightweight and do not update the progress tracker.

### Rules
- **Do not make architectural or design decisions.** If a step is ambiguous during execution, **stop** — present concrete interpretations, give your recommendation with reasoning, and ask the user to confirm before proceeding.
- Do not add features, improvements, or refactors that are not in the plan.
- Do not bypass safety checks or destructive operations without explicit confirmation in the plan.
- Check off steps only after all verification passes, not during implementation.

---

## Phase 4 — Fix

**Trigger:** `Fix: [what to fix]`

This phase can be used in two ways, and **where the fix is documented depends on which**:
- **Standalone** — A fix requested on its own. Document it as a new fix doc in `.agents/fix/NNN-[fix-name].md` (see **Fix Documents**), then move it through the same Plan → Refine → Implement lifecycle as a feature. Small, unambiguous fixes may be planned and implemented in a single pass.
- **Escalated from Implement** — If an Implement step surfaces a small defect that must be resolved to continue, document it **inline in the active feature plan** (not as a separate fix doc), apply the fix, then return to Implement.

### Objective
Apply a narrow, well-scoped fix to an existing issue, with a lightweight record of what was done. No architectural decisions, no new features, no refactoring beyond what is required to resolve the stated problem.

### Steps
1. Determine whether the fix is **standalone** or **escalated**, and pick the documentation target accordingly (fix doc in `.agents/fix/` vs. inline in the active feature plan).
2. Read the affected files to understand the current state.
3. Identify the exact root cause of the issue described. Optionally, establish a confirming check including a failing test, a concrete reproduction step, or a specific error message if needed.
4. Record the fix in the chosen document — scope, root cause, and the steps to apply it — following the structure in `.agents/feature/NNN-example.md`.
5. Apply the fix, scoped strictly to what was described. Then, run any relevant verification steps to confirm the fix works and does not introduce new issues.
6. Report what was changed and why. Fixes do **not** update `.agents/context/progress-tracker.md`.

### Rules
- No refactoring beyond the fix.
- **No design or architectural decisions.** If the fix requires a design decision or touches more than the stated scope, present a recommendation and ask the user to confirm before proceeding.
- Standalone fixes are documented in `.agents/fix/`; escalated fixes are documented inline in the feature plan that triggered them.
- Update relevant feature plans when the fix resolves a tracked blocker.

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