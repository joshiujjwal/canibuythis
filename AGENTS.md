# AGENTS.md — CanIBuyThis

Standard agent instructions for Codex and autonomous coding agents.

---

## Setup

```bash
cd canibuythis
npm install
npm run test:run    # Verify green baseline before touching anything
```

If `npm install` fails, check Node version: requires Node 18+.

---

## Repository Layout

```
src/utils/budgetEngine.ts   ← Core calculation logic (pure TS, no React)
src/types/index.ts          ← All shared TypeScript interfaces
src/store/                  ← Zustand state management
src/components/             ← React components
tests/                      ← All test files (mirror src/ structure)
docs/spec.md                ← Feature specification and data models
TODO.md                     ← Phased task list with evidence gates
```

Always read `docs/spec.md` before implementing anything — it defines the data models and algorithm.

---

## Code Style

- **TypeScript strict mode** — no `any`, no type assertions without a comment explaining why
- **Functional components only** — no class components
- **Named exports** — no default exports (makes refactoring easier)
- **Pure utils** — `src/utils/` files must not import from React or Zustand
- **Tailwind only** — no CSS modules, no styled-components, no inline styles
- Strings: double quotes in TSX, single quotes in TS
- Semicolons: yes
- Line length: 100 chars max

---

## Testing (Red/Green TDD)

```bash
npm run test:run       # Run all tests once
npm test               # Watch mode during development
```

**Mandatory workflow**:
1. Write failing test(s) first — commit as `test: describe what you're testing`
2. Implement to make tests pass — commit as `feat:` or `fix:`
3. Never comment out or delete a failing test to get CI green — fix the underlying issue

**Test conventions**:
- `describe` block = the unit under test
- `it` / `test` block = one behavior, one assertion per test preferred
- Use `@testing-library/user-event` for user interactions, not `fireEvent`
- Avoid `act()` wrappers — RTL handles this automatically

---

## PR Instructions

Every PR must include:

1. **Test output**: Paste `npm run test:run` output showing all tests pass
2. **What was changed and why**: One paragraph description
3. **Manual test evidence**: Screenshot or terminal log showing the feature works
4. **TODO.md updated**: Check off completed tasks

Do NOT:
- Bundle multiple features in one PR
- Merge if any test is failing or skipped
- Add `// @ts-ignore` without a comment explaining why it's necessary
- Add new dependencies without updating CLAUDE.md and checking with the team

---

## Environment

- Node 18+ required
- No environment variables needed (pure client-side)
- No .env files needed
- Build output goes to `dist/` — do not commit this directory
