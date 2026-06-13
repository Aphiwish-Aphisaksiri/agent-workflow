# agent-workflow

A phase-driven AI agent workflow system for structured software development. It enforces a disciplined Plan → Refine → Implement → Fix cycle so that AI agents never make undocumented design decisions or drift out of scope.

---

## How It Works

Every agent interaction begins with a **phase prefix** in the prompt. The agent detects the phase and follows the corresponding instructions from `AGENTS.md`.

| Prompt prefix | Phase | What happens |
|---|---|---|
| `Plan: [goal]` | **Plan** | Translates a goal into a complete, unambiguous feature plan |
| `Refine: [plan]` | **Refine** | Stress-tests an existing plan, fills gaps, resolves open questions |
| `Implement: [plan]` | **Implement** | Executes the approved plan — no new decisions |
| `Fix: [issue]` | **Fix** | Applies a narrow, targeted fix without scope creep |
| `Meta: [goal]` | **Meta** | Improves the workflow system itself |

---

## Directory Structure

```
AGENTS.md                        ← Phase definitions and rules (the "brain")
README.md                        ← This file
.agents/
  context/
    ai-workflow-rules.md         ← Project-specific overrides (read first, every session)
    project-context.md           ← Project name, description, users, key workflows
    architecture.md              ← Tech stack, structure, data flow
    code-standards.md            ← Naming conventions, file organization, testing
    ui-context.md                ← Component library, theme, layout patterns
    progress-tracker.md          ← Current phase, feature checklist, blockers
  docs/
    README.md                    ← Index of reference docs (API specs, research, etc.)
    ...                          ← Additional reference documents
  feature/
    NNN-example.md               ← Annotated example plan (required reading for Plan/Refine)
    NNN-[feature-name].md        ← Feature plans (numbered sequentially)
```

---

## Getting Started

### 1. Set up your AI agent

Copy or symlink `AGENTS.md` to the location your agent platform reads:

| Platform | File location |
|---|---|
| GitHub Copilot | `.github/copilot-instructions.md` |
| Cursor | `.cursorrules` |
| Other | `AGENTS.md` (root) |

### 2. Configure context files

You can do this manually **or** let the agent do it for you.

**Option A — Assisted setup (recommended for new projects)**

Run the following in your agent:

```text
Read AGENTS.md and Implement: 000
```

*(Note: Adding "Read AGENTS.md" ensures the agent knows how to process the `Implement` command if your IDE doesn't automatically load the rules file yet.)*

Optionally add a brief description of your project, users, and tech stack after `000` — or leave it empty and the agent will ask. Either way, it interviews you for any missing information and writes the answers directly into `.agents/context/`, so every future session starts with full context.

You can also drop reference documents (API specs, design docs, etc.) into `.agents/docs/` and mention them in your prompt to have the agent pull them in during setup.

**Option B — Manual setup**

Fill in the files under `.agents/context/` with your project's details. At minimum, populate:
- `project-context.md` — what the project is and who uses it
- `architecture.md` — your tech stack and folder structure
- `ai-workflow-rules.md` — any project-specific rules that override default agent behavior

### 3. Keep agent files local (optional)

If you treat agent workflow files as personal tooling (not shared with the team), add them to `.git/info/exclude` so they never appear in diffs or get pushed to remote:

```text
# .git/info/exclude
AGENTS.md
.agents/
```

> **Why `.git/info/exclude` instead of `.gitignore`?** It's per-repo and local-only — no commit required, no noise in PRs, invisible to collaborators. Perfect for files that are useful to you but not necessarily the team.

If you run the assisted setup, the agent will ask you about this directly.

### 4. Start a session

Begin any prompt with the appropriate phase prefix:

```
Plan: add user authentication with email and password
```

The agent will read context files, ask clarifying questions if needed, and write a plan to `.agents/feature/`.

---

## Phase Flow

```
Plan ──→ Refine ──→ Implement
                       │
                       └──→ Fix (for defects found during implementation)

Meta (anytime, for improving the workflow itself)
```

A plan must have an empty **Open Questions** section before it can be handed to Implement. The Refine phase exists specifically to get it there.

---

## Key Rules

- **Implement never makes design decisions.** If a decision is required and cannot be safely inferred, the agent stops and asks the user to run a Refine pass.
- **Fix never refactors beyond the stated issue.** If the scope is larger than expected, the agent stops and reports before continuing.
- **All decisions are documented.** Every choice made during Plan is recorded under **Decisions Made** in the plan file — nothing is left implicit.
- **Progress is tracked.** After each Implement session, `.agents/context/progress-tracker.md` is updated.

---

## Compatibility

`AGENTS.md` is a plain Markdown file with no runtime dependencies. It works with any AI agent or LLM that accepts a system prompt or instructions file.
