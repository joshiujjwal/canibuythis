# GitHub Copilot Instructions — CanIBuyThis

## Project Context

CanIBuyThis is a client-side React + TypeScript app that answers "can I buy this today?" based on the user's budget goals, spending velocity, and upcoming financial obligations. No backend. No bank linking. All data lives in localStorage.

---

## Stack

- **React 18** with functional components and hooks only
- **TypeScript** with strict mode — no `any` types
- **Vite** for build tooling
- **Tailwind CSS** for all styling — no CSS files, no inline styles
- **Zustand** for state management with localStorage persistence
- **Vitest + React Testing Library** for tests

---

## Coding Conventions

### TypeScript
- All interfaces defined in `src/types/index.ts` and imported from there
- Use `interface` for object shapes, `type` for unions/aliases
- Strict null checks are on — handle `null` and `undefined` explicitly
- No `any`. If you're tempted to use `any`, use `unknown` and narrow it

### React
- Functional components with named exports only
- Props interface defined in the same file as the component
- Custom hooks in `src/hooks/`, prefixed with `use`
- No class components, no HOCs

### State
- Zustand stores in `src/store/`, one file per domain
- All stores use `persist` middleware — storage key prefix `canibuythis_`
- Store actions are defined inside the store create callback (not outside)

### Budget Engine
- `src/utils/budgetEngine.ts` must stay pure — no React, no Zustand, no DOM
- All calculation functions are exported named functions, not methods on a class
- Every exported function has a JSDoc comment explaining its purpose and parameters

---

## Testing Conventions

- Tests in `tests/` mirror the structure of `src/`
- Test file naming: `ComponentName.test.tsx` or `functionName.test.ts`
- Use `@testing-library/user-event` for all user interactions
- Assert on visible text and ARIA roles, not implementation details (no `.querySelector`)
- No snapshots — they're brittle and don't describe behavior
- Every new utility function must have tests before the PR is merged

---

## Boundaries

- **Do not** add new npm packages without checking `TODO.md` Parking Lot first
- **Do not** refactor working code unless the task explicitly requires it
- **Do not** add any server-side code, APIs, or network requests
- **Do not** store sensitive financial data beyond what the user explicitly enters
- **Do not** remove or skip existing tests
- **Do not** use `localStorage` directly — always go through Zustand's persist middleware
