# Feature 000: Populate Agent Workflow Files

Status: Ready

## Goal

Provide a reusable, step-by-step blueprint that any agent can follow to fully populate the `.agents/context/` files, `.agents/docs/README.md`, and `AGENTS.md` for a new project by interviewing the user with targeted, per-field questions and writing the files once all answers are collected.

## Background / Context

The agent workflow system relies on several context files that agents read at the start of every interaction. When a new project is bootstrapped, these files contain empty placeholders. This blueprint ensures they are populated accurately and completely before any development work begins.

## Depends on (if any)

- The `.agents/` folder structure must already be scaffolded (empty template files in place)

## Out of Scope

- Writing any application code
- Creating feature plans (those follow the normal Plan phase)
- Populating `.agents/docs/` with actual reference document files (only the `README.md` index is updated here)
- Making architectural decisions on behalf of the user without explicit permission

## Affected Files

- `AGENTS.md` — updated only if the user specifies project-level overrides
- `.agents/context/ai-workflow-rules.md` — workflow mode, development order, restrictions
- `.agents/context/project-context.md` — project name, description, users, workflows, setup
- `.agents/context/architecture.md` — stack, structure, request flow, patterns
- `.agents/context/code-standards.md` — naming, organization, testing, tooling
- `.agents/context/ui-context.md` — component library, CSS approach, theme, layout patterns
- `.agents/context/progress-tracker.md` — current phase, checklist, blockers, next steps
- `.agents/docs/README.md` — reference document index

## Design Decisions

- **Interview style:** One batch of all questions upfront — a second round is allowed only if user answers introduce new gaps, not as a routine follow-up.
- **Question depth:** Specific, per-field questions matching each placeholder section in the target file.
- **Fallback — minor/undecided field:** Leave as placeholder comment; record as an open question in `progress-tracker.md` if non-blocking.
- **Fallback — critical missing info:** Record as a blocker in `progress-tracker.md`.
- **Agent autonomy:** If the user explicitly says the agent may decide a minor detail, pick the best option based on reasoning, document the decision inline, and note it in the post-write summary.
- **Existing content — merge:** Fill empty sections and leave already-populated sections intact by default.
- **Existing content — conflict:** If an incoming answer conflicts with existing content, pause and ask the user to resolve before writing that file.
- **Output timing:** Write all files immediately once all answers are processed, then post a summary to chat.

---

## Implementation Steps

### Phase A: Read & Assess

- [ ] Read all existing context files and `AGENTS.md`
- [ ] Identify which sections are already populated vs. empty placeholder
- [ ] Note any existing content that may conflict with incoming answers

### Phase B: Interview the User

Ask **all questions below in a single batch**. Group them clearly by file so the user can answer in any order. If a question does not apply to the project, the user may skip it.

---

#### `AGENTS.md`

1. Does the standard four-phase workflow (Plan → Refine → Implement → Meta) fit your project, or do you need custom phases or renamed triggers?
2. Are there any project-level conventions that should be added to `AGENTS.md`? *(e.g., a rule about shared components, a branching strategy, a mandatory review step)*

---

#### `ai-workflow-rules.md`

3. What mode is this project starting in? *(Mockup/Prototype · Development · Maintenance)*
4. What should be built first? Describe the development order or priority.
5. Are there any hard restrictions — things agents must **never** do on this project? *(e.g., no backend logic in mockup mode, no paid third-party services)*
6. Are there any phase-specific workflow overrides? *(e.g., skip Refine for small UI tweaks, always ask before touching shared components)*
7. Any other behavioral rules for agents on this project?

---

#### `project-context.md`

8. What is the project name?
9. What does the project do? *(2–3 sentences: domain, main user action, output)*
10. What problem or pain point does it solve?
11. Who are the target users? *(technical level, what they care about)*
12. What are the 3–5 most important user workflows — the flows that must never break?
13. What are the prerequisites? *(Node version, Python version, required global tools)*
14. How do you install and run the project locally? *(commands)*
15. What environment variables are required and what does each do? *(names and purpose only — no real values)*
16. What are the key files or entry points agents should know about?
17. What are the hard constraints? *(non-negotiables, e.g., "must support mobile", "WCAG AA accessibility", "no paid APIs")*

---

#### `architecture.md`

18. What is the frontend framework or approach?
19. What is the backend framework or runtime?
20. What database type and provider are you using?
21. What authentication solution are you using?
22. Where is the project hosted or deployed?
23. What notable third-party services or integrations are in use?
24. Describe the top-level project directory structure and each folder's purpose.
25. How does a typical request flow from a user action to data storage and back?
26. Are there any key design patterns in use? *(e.g., repository pattern, event-driven, CQRS, MVC)*

---

#### `code-standards.md`

27. What file and folder naming convention is used? *(e.g., kebab-case for files, PascalCase for components)*
28. What is each major directory responsible for?
29. What naming conventions apply to components, functions, variables, and types?
30. What coding patterns should agents always follow? *(e.g., always use async/await, prefer functional components, no class components, immutable state)*
31. What testing approach is used? *(framework, what gets tested, coverage expectations)*
32. What linting, formatting, or commit tooling is in use? *(ESLint, Prettier, Husky, lint-staged, etc.)*

---

#### `ui-context.md`

33. What component library or design system is in use, if any? *(e.g., shadcn/ui, Material UI, custom)*
34. What CSS approach is used? *(Tailwind, CSS modules, styled-components, plain CSS)*
35. What is the color scheme or theme? *(primary, surface, accent colors, or design token system)*
36. What layout patterns does the app use? *(sidebar, top nav, grid, full-page sections)*
37. What are the key behavioral patterns? *(how modals open/close, loading states, error states, empty states)*

---

#### `progress-tracker.md`

38. What is the current phase of the project? *(planning, early development, in-progress, maintenance)*
39. What is the initial feature or task checklist? *(list the features to be built)*
40. Are there any known blockers right now?
41. What are the immediate next steps?

---

#### `.agents/docs/README.md`

42. Are there any reference documents to add to the index? *(API specs, design docs, research notes, external references)* — If yes, provide a filename and one-line description for each.

### Phase C: Process Answers

- [ ] For each answered field: prepare the merged content for that file section
- [ ] For each unanswered/deferred field:
  - If blocking → add to `progress-tracker.md` blockers, leave field as placeholder
  - If non-blocking → add to `progress-tracker.md` open questions, leave field as placeholder
- [ ] For any conflict with existing content: stop and ask the user to resolve before writing that file
- [ ] If agent is permitted to decide a minor detail: make the decision, note it inline in the file, and include it in the Phase E summary

### Phase D: Write Files

- [ ] Write `.agents/context/ai-workflow-rules.md`
- [ ] Write `.agents/context/project-context.md`
- [ ] Write `.agents/context/architecture.md`
- [ ] Write `.agents/context/code-standards.md`
- [ ] Write `.agents/context/ui-context.md`
- [ ] Write `.agents/context/progress-tracker.md`
- [ ] Update `.agents/docs/README.md` index
- [ ] Update `AGENTS.md` only if the user specified overrides in questions 1–2

### Phase E: Summary & User Verification

- [ ] Post a chat summary listing:
  - Files written and their completion status *(full / partial)*
  - Fields left as placeholder and the reason
  - Any conflicts resolved or still pending user input
  - Agent decisions made autonomously *(if any)*
- [ ] Wait for user to review and confirm the summary
- [ ] If user provides feedback or change requests, apply corrections before marking complete

---

## Open Questions (if any)

*(None — all decisions resolved before writing this plan.)*

---

## Blockers (if any)

*(None.)*

---

## Verification

- **Completeness:** All context files have no remaining empty placeholders (unless explicitly deferred and tracked in `progress-tracker.md`).
- **Accuracy:** `progress-tracker.md` accurately reflects the current project state, open questions, and blockers.
- **Consistency:** No unresolved conflicts between files remain.
- **User approval:** Summary posted to user; user has reviewed and confirmed the populated content. If user gives feedback to change, corrections must be applied before this feature is marked complete.
