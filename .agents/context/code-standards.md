# Code Standards

> **Placeholder** — Coding conventions for AI agents on this project.
> When starting a new project, ask the agent: `Meta: populate all context files in .agents/context/ based on this project`

---

## File Organization

<!-- How are files and folders structured within the source tree? e.g.:
- Feature-based folders: `src/features/auth/`, `src/features/dashboard/`
- Co-locate tests next to source: `UserCard.tsx` + `UserCard.test.tsx`
- Barrel exports via `index.ts` per folder (or forbidden — pick one)
- Where do types live: co-located or in `src/types/`?
-->

---

## Naming Conventions

### Files & Folders
<!-- e.g., PascalCase for components, kebab-case for pages, camelCase for utilities -->

### Variables & Functions
<!-- e.g., camelCase for variables/functions, UPPER_SNAKE for constants, PascalCase for classes/types -->

### Components
<!-- e.g., named exports only, no default exports, suffix with purpose: `UserCard`, `useUserStore`, `userSchema` -->

### Database / API
<!-- e.g., snake_case for DB columns, camelCase for API response fields, plural resource names in routes -->

---

## Coding Patterns

### Preferred Patterns
<!-- Patterns to use consistently. e.g.:
- Prefer `const` arrow functions over `function` declarations for components
- Prefer early returns over nested conditionals
- Use `zod` for all runtime validation
- All async operations wrapped in try/catch at the service layer
-->

### Anti-Patterns
<!-- Patterns explicitly forbidden. e.g.:
- No `any` type in TypeScript
- No inline styles
- No direct DOM manipulation
- No business logic in components
-->

---

## Mock Data Standards

<!-- How is mock/test data managed. e.g.:
- All mock data lives in `src/mocks/`
- Use `faker.js` for generated data, seed with fixed value for reproducibility
- Mock files named `[entity].mock.ts` (e.g., `user.mock.ts`)
- Never commit real user data, even for testing
-->

---

## Testing

### What to Test
<!-- e.g., unit test all utility functions, integration test API routes, e2e test critical user flows -->

### What Not to Test
<!-- e.g., no snapshot tests, don't test implementation details -->

### Test File Location
<!-- e.g., co-located with source (`*.test.ts`) or in `__tests__/` folders -->

### Naming
<!-- e.g., `describe('ComponentName')` → `it('does X when Y')` -->

---

## Import Conventions

<!-- e.g.:
- Use path aliases (`@/components/`) over relative paths beyond 1 level
- Group imports: built-ins → external packages → internal modules → styles
- No circular imports across module boundaries
-->

---

## Error Handling

<!-- e.g.:
- User-facing errors shown via toast (not console.log)
- Unexpected errors logged to monitoring service
- All thrown errors must be typed (no `throw 'string'`)
-->
