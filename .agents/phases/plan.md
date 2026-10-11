# Phase — Plan

**Trigger:** `Plan: [goal]`

## Objective
Translate a high-level goal into a complete, unambiguous plan document that can be handed to Implement without further design decisions.

## Steps
1. Read `.agents/feature/NNN-example.md` for the plan structure and conventions.
2. Analyze the goal and identify missing information, ambiguous requirements, and decisions that must be made before a solid plan can be written.
3. **If gaps exist:** Consolidate all questions into a single response and ask the user. Wait for answers before writing the plan. Ask follow-ups only if the user's answers introduce new gaps — not as routine rounds. Include the **tracker ticket** in this batch if the user has not already supplied one.
4. Write the plan following the structure defined in `NNN-example.md`. Use `.agents/feature/NNN-[feature-name].md` for features, or `.agents/fix/NNN-[fix-name].md` for standalone fixes (see **Fix Documents** in `AGENTS.md`).
5. Summarize the plan and list any items left as open questions.

## Rules
- Do not write any code or make file changes during this phase.
- Never leave a decision implicit — if you make a call, record it under **Decisions Made**.
- A plan is ready for Implement only when **Open Questions** is empty.
- **Always fill in the `Ticket:` line.** Ask the user for it if it is not obvious. Never invent a ticket ID — if the work has no ticket, record `none` explicitly so the absence is deliberate.
