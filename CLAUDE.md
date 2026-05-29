# CLAUDE.md — CanIBuyThis

Context file for AI coding assistants. Keep under 200 lines. Update when conventions change.

---

## Commands

```bash
npm run dev          # Start Vite dev server (http://localhost:5173)
npm test             # Run Vitest in watch mode
npm run test:run     # Run tests once (for CI)
npm run test:ui      # Vitest UI (browser-based test explorer)
npm run lint         # ESLint
npm run format       # Prettier write
npm run build        # Production build → dist/
npm run preview      # Preview production build locally
```

**Always run `npm test` first before making changes.** Start from green.

---

## Directory Map

```
src/
  components/     React UI components — one file per component, co-located types
  hooks/          Custom hooks — prefix with "use", one hook per file
  store/          Zustand slices — one file per domain (profile, spending, goals, upcoming)
  utils/          Pure functions, no React — budgetEngine.ts is the core logic
  types/          Shared TypeScript interfaces — index.ts exports everything
tests/
  components/     RTL tests matching src/components/ structure
  hooks/          Hook tests using renderHook
  utils/          Pure unit tests — no DOM needed
docs/
  spec.md         The source of truth for what the app is supposed to do
  adr/            Architecture Decision Records for non-obvious choices
```

---

## Key Conventions

### Budget Engine is Pure
`src/utils/budgetEngine.ts` must remain pure TypeScript — no React imports, no DOM, no store access. It takes data in, returns a `PurchaseVerdict`. This makes it trivially testable.

### Zustand Store Structure
Each store slice follows this pattern:
```typescript
interface ProfileStore {
  profile: FinancialProfile | null;
  setProfile: (p: FinancialProfile) => void;
  clearProfile: () => void;
}
```
Persistence is added via `persist` middleware from `zustand/middleware`. Storage key prefix: `canibuythis_`.

### Component Rules
- Props interfaces defined in the same file as the component
- No inline styles — Tailwind classes only
- No `any` types — ever
- Error states must be handled (empty state, loading state, error state = the three states)

### Test File Naming
- `tests/utils/budgetEngine.test.ts` → mirrors `src/utils/budgetEngine.ts`
- `tests/components/VerdictCard.test.tsx` → mirrors `src/components/VerdictCard.tsx`

### Verdict Color System
| Verdict | Tailwind classes |
|---------|-----------------|
| yes | `bg-green-50 border-green-500 text-green-800` |
| maybe | `bg-yellow-50 border-yellow-500 text-yellow-800` |
| no | `bg-red-50 border-red-500 text-red-800` |

---

## Gotchas

- **Pay period math**: Monthly income ÷ 4.33 ≠ weekly income. Use `WEEKS_PER_MONTH = 52/12` for accuracy.
- **Date handling**: All dates stored as ISO strings. Never store `Date` objects in state — they don't serialize cleanly to localStorage.
- **localStorage quota**: ~5MB limit. Export/import for backup — don't store transaction history indefinitely.
- **Vitest globals**: configured in `vite.config.ts` — no need to import `describe`/`it`/`expect` in test files.

---

## Workflow

1. Read `TODO.md` — pick the next unchecked task in the current phase
2. Run `npm test` — confirm you're starting from green
3. Write the test first in `tests/` (red phase)
4. Implement in `src/` until tests pass (green phase)
5. Run `npm run lint` — fix any issues
6. Review `git diff` manually
7. Commit: `feat:` or `test:` or `fix:` prefix
8. Update this file if you discovered a non-obvious convention
9. Update `TODO.md` — check off the task, add to Lessons Learned if needed

---

## Boundaries

- Do not refactor working code unless the task explicitly asks for it
- Do not remove tests even if they seem redundant
- Do not add external libraries without checking the Parking Lot in TODO.md first
- Do not add backend infrastructure — this is a pure client-side app in v0.1
