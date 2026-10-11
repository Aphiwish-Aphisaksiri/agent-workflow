# Agent Workflow

This file defines the shared conventions and phase routing for AI agents on this project.
When a phase is active, agents load the corresponding phase file from `.agents/phases/`.
When no phase is active, agents respond naturally using codebase knowledge and context files.

---

## Phase Detection

| Prompt prefix | Phase | Instruction file | Purpose |
|---|---|---|---|
| `Plan: [goal]` | **Plan** | `.agents/phases/plan.md` | Translate a goal into a structured feature plan |
| `Refine: [feature or plan file]` | **Refine** | `.agents/phases/refine.md` | Stress-test and strengthen an existing plan |
| `Implement: [feature or plan file]` | **Implement** | `.agents/phases/implement.md` | Execute an approved plan or fix doc — no new decisions |
| `Fix: [what to fix]` | **Fix** | `.agents/phases/fix.md` | Document and apply a targeted fix — standalone in `.agents/fix/`, or inline in a feature plan when escalated from Implement |
| `Meta: [goal]` | **Meta** | `.agents/phases/meta.md` | Improve this workflow system itself |

> **Mandatory:** When a phase is triggered, **read the instruction file first before taking any action or planning steps.** Do not proceed from memory or guess the phase rules.

### No phase detected

If no phase keyword is present and the intent does not clearly map to a phase above, **do not force phase detection.** Instead:

- Respond naturally — no phase rules apply.
- Use context files and codebase knowledge as usual to give informed answers.
- If the conversation surfaces something actionable (a bug, a feature idea, a decision), *suggest* escalating to the appropriate phase — but do not force it or ask unprompted "which phase should we use?"

---

## Context Files

**Every interaction, read `.agents/context/ai-workflow-rules.md` first.** This file contains project-specific rules that may override default phase behavior.

Then, read any other context files relevant to the current task. Do not assume their current state.

**Before any interaction with an external tool or service — source control host, CI, code quality, issue tracker, or an MCP server — read `.agents/context/tools.md` first.** It records the verified identifiers, access methods, and gotchas. Do not guess a project key, pipeline slug, or branch name, and do not fall back to a different tool than the one specified.

| File | Contents |
|---|---|
| `.agents/context/project-context.md` | Project name, description, users, key workflows, setup |
| `.agents/context/architecture.md` | Tech stack, project structure, page architecture, data flow |
| `.agents/context/code-standards.md` | File organization, naming conventions, coding patterns, testing |
| `.agents/context/ui-context.md` | Component library, theme, color scheme, layout and behavior patterns |
| `.agents/context/tools.md` | External tools & services: access methods, credentials policy, gotchas |
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

The `NNN` number is an **internal handle** — it is meaningless outside `.agents/`. Every plan and fix doc therefore carries a `Ticket:` line recording the associated tracker item (Jira, GitHub issue, Linear, board card, or `none`), which is the identifier the rest of the team can actually open.

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

## Output Hygiene — Internal References

Internal workflow artifacts must not leak into anything other people see. This covers two kinds of internal reference: the plan/fix **numbering** and internal **file paths**. Both are meaningless — or unopenable — to anyone outside this repo's `.agents/` folder. Treat this as a strong default: binding, not advisory.

**Externally-visible surfaces** (where these rules apply): git commit messages, PR titles and descriptions, code comments and docstrings, and external docs (Confluence, Notion, etc.).

**Internal surfaces** (exempt — keep references as-is): files under `.agents/` and chat with the user. These *rely* on the numbering and paths for cross-referencing. Do not scrub them.

### Plan/fix numbering

- **Never emit a bare `NNN` plan/fix number to an externally-visible surface.** A reviewer reading "builds on 008" has no way to resolve it.
- **Translate to the tracker ticket instead.** Every plan carries a `Ticket:` line — refer to the work by that identifier.
- **If a plan has no ticket or the mapping is unclear, ask the user** before emitting. Do not silently fall back to a plain-language description; only describe the work in plain words once the user confirms there is no ticket.

### Internal file paths

- **Never cite a path the reader cannot open.** The test is whether the path is **committed and visible to the reader** — not merely whether it exists locally. Anything git-excluded or uncommitted fails this test: `.agents/**` when it is excluded, local scratch files, `.env`, and so on.
- **If the content also lives in a committed artifact, cite that instead.** Pointing at real source paths is encouraged — reviewers want them.
- **If there is no committed counterpart, drop the path entirely** and describe the substance on its own merits. Do not mention where the content is stored or edited. Still surface any real consequence the reader needs — for example a required deployment or coordination step must be described as an action, without naming the internal doc that tracks it.

> **Note:** If a project deliberately commits `.agents/` so the team shares it, those paths are visible and may be cited. The rule keys on reader visibility, not on the folder name.
