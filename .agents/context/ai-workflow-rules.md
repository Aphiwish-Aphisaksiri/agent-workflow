# AI Workflow Rules

> This file contains project-specific rules that override default agent behavior.

---

## Project Mode

<!-- What mode is this project in? Choose one and remove the others, then expand with specifics. -->

- **Mockup / Prototype** — Visual-first. Focus on UI and interactions. Do not build backend logic.
- **Development** — Full-stack feature development. Follow normal Plan → Refine → Implement flow.
- **Maintenance** — Bug fixes and incremental changes to an existing system. Minimize surface area of changes.

---

## Development Order

<!-- Define the priority order for what gets built first. Example below. -->

<!-- **Mockup mode example:**
1. Page layouts and navigation structure
2. Component visuals and static states
3. Interactive states (hover, active, loading skeletons)
4. Mock data wiring
5. Backend integration (only when mode changes to Development)
-->

---

## Restrictions

<!-- What is explicitly NOT allowed in this project, regardless of what a plan says. -->

<!-- **Mockup mode example:**
- Do not create API routes or server-side logic
- Do not connect to a real database
- Do not write authentication logic
- All data must come from mock/static sources
-->

---

## Workflow Overrides

<!-- Any phase-specific overrides. E.g., "skip Refine for small UI tweaks", "always ask before touching shared components" -->

<!-- Fixes: by default a standalone fix gets its own doc in `.agents/fix/` and
runs the full Plan → Refine → Implement cycle. Common overrides:
- Skip Refine for fixes under N lines of change
- Always run [build/lint/test command] as fix verification
-->

<!-- Output hygiene: `AGENTS.md > Output Hygiene — Internal References` already
bans internal plan numbers and unopenable paths from commits, PRs, and external
docs. Tighten it here if this project needs specifics, e.g.:
- Name the tracker to translate plan numbers into (e.g. "always cite the Jira key")
- Add project-specific surfaces that count as externally visible
- Note if `.agents/` is committed here, making those paths citable
-->


---

## Notes

<!-- Any other project-specific behavioral rules that don't fit the above categories. -->
