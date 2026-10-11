# Phase — Implement

**Trigger:** `Implement: [feature name, plan file, or description]`

## Objective
Execute the approved plan faithfully. No design decisions, no scope additions.

## Steps
1. Read the target plan from `.agents/feature/` (feature plans) or `.agents/fix/` (fix docs). Verify **Open Questions** is empty. If not, stop and ask the user to run a Refine pass first.
2. Execute all implementation steps in the plan in order.
3. Run the **Verification** steps from the plan — build, lint, type-check, tests, integration / smoke-test, and regression as applicable. If any check fails, fix the issue before proceeding.
4. Update the plan file — Check off all completed steps (`- [x]`). Only do this after all verification passes.
5. Report a summary of what was done.
6. Update `.agents/context/progress-tracker.md`. **Exception:** skip this step when implementing a fix doc from `.agents/fix/` — fixes are lightweight and do not update the progress tracker.

## Rules
- **Do not make architectural or design decisions.** If a step is ambiguous during execution, **stop** — present concrete interpretations, give your recommendation with reasoning, and ask the user to confirm before proceeding.
- Do not add features, improvements, or refactors that are not in the plan.
- Do not bypass safety checks or destructive operations without explicit confirmation in the plan.
- Check off steps only after all verification passes, not during implementation.

---

## Post-Implementation Fixes

After implementation is complete, the user may test the result and come back with small changes or defects to fix. When this happens:

1. Apply the fix, scoped strictly to what the user described.
2. Run any relevant verification steps to confirm the fix works and does not introduce new issues.
3. **Document the fix as a post-implementation fix round in the plan file**, using the same blockquote style as Refine rounds. Add it at the top of the plan, below the last Refine round entry:

```md
> **Post-implementation fix (round 1) — YYYY-MM-DD:** [What the user reported or requested].
> Root cause: [What caused the issue].
> Fix: [What was changed and where].
```

4. Number rounds sequentially (`round 1`, `round 2`, etc.) just like Refine rounds.

Post-implementation fixes are lightweight — they do **not** update `.agents/context/progress-tracker.md`.
