# Phase — Fix

**Trigger:** `Fix: [what to fix]`

This phase can be used in two ways, and **where the fix is documented depends on which**:
- **Standalone** — A fix requested on its own. Document it as a new fix doc in `.agents/fix/NNN-[fix-name].md` (see **Fix Documents** in `AGENTS.md`), then move it through the same Plan → Refine → Implement lifecycle as a feature. Small, unambiguous fixes may be planned and implemented in a single pass.
- **Escalated from Implement** — If an Implement step surfaces a small defect that must be resolved to continue, document it **inline in the active feature plan** (not as a separate fix doc), apply the fix, then return to Implement.

## Objective
Apply a narrow, well-scoped fix to an existing issue, with a lightweight record of what was done. No architectural decisions, no new features, no refactoring beyond what is required to resolve the stated problem.

## Steps
1. Determine whether the fix is **standalone** or **escalated**, and pick the documentation target accordingly (fix doc in `.agents/fix/` vs. inline in the active feature plan).
2. Read the affected files to understand the current state.
3. Identify the exact root cause of the issue described. Optionally, establish a confirming check including a failing test, a concrete reproduction step, or a specific error message if needed.
4. Record the fix in the chosen document — scope, root cause, and the steps to apply it — following the structure in `.agents/feature/NNN-example.md`.
5. Apply the fix, scoped strictly to what was described. Then, run any relevant verification steps to confirm the fix works and does not introduce new issues.
6. Report what was changed and why. Fixes do **not** update `.agents/context/progress-tracker.md`.

## Rules
- No refactoring beyond the fix.
- **No design or architectural decisions.** If the fix requires a design decision or touches more than the stated scope, present a recommendation and ask the user to confirm before proceeding.
- Standalone fixes are documented in `.agents/fix/`; escalated fixes are documented inline in the feature plan that triggered them.
- Update relevant feature plans when the fix resolves a tracked blocker.
