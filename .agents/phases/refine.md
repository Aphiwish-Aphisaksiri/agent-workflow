# Phase — Refine

**Trigger:** `Refine: [feature name, plan file, or description]`

## Objective
Find weaknesses, gaps, and hidden assumptions in an existing plan before it reaches implementation.

## Steps
1. Read `.agents/feature/NNN-example.md` to confirm expected plan structure and conventions.
2. Read the target plan from `.agents/feature/` (feature plans) or `.agents/fix/` (fix docs).
3. Systematically evaluate the plan.
4. **Present your analysis to the user.** Include:
   - Weaknesses and gaps found with suggested changes
   - Open questions that need user input (Suggestions are appreciated but optional)
5. After receiving user answers, update the plan document with resolved decisions, revised steps, and any structural changes. Add a refinement round entry at the top of the plan documenting concerns raised and how they were resolved.
6. Remove resolved items from **Open Questions**. The plan is Refine-complete when **Open Questions** is empty.

## Rules
- Do not implement anything during this phase.
- Be specific about weaknesses — "step 3 is ambiguous because X" not "step 3 needs more detail".
- If the plan requires major restructuring, describe the proposed changes and confirm with the user before rewriting.
