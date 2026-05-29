# CanIBuyThis — Task Breakdown

## How to Use This File

Workflow per task:
1. Write the test FIRST (red phase) — commit as "test: <description>"
2. Implement until tests pass (green phase) — commit as "feat: <description>"
3. Review your diff manually — no surprises allowed
4. Commit with a descriptive message
5. If you learned something, add it to the **Lessons Learned** section below
6. Update CLAUDE.md or AGENTS.md if a convention changed

**Evidence gate**: Each phase is locked until all tasks in it have passing tests AND a manual review note. Don't skip forward.

---

## Phase 0: Foundation ⬜

- [ ] Init Vite + React + TypeScript project (`npm create vite@latest`)
- [ ] Configure Tailwind CSS
- [ ] Configure ESLint + Prettier with project rules
- [ ] Set up Vitest + React Testing Library (`npm install -D vitest @testing-library/react @testing-library/user-event jsdom`)
- [ ] Write first smoke test: `App renders without crashing`
- [ ] Configure GitHub Actions CI: lint + test on every push/PR
- [ ] Verify `.gitignore` covers `node_modules`, `dist`, `.env*`
- [ ] Review all AI config files (CLAUDE.md, AGENTS.md, copilot-instructions.md) — adjust to reflect actual project

**Evidence gate**: CI green on main, smoke test passing ✅

---

## Phase 1: Data Model & Budget Engine ⬜

Core: pure TypeScript calculation logic. No UI yet.

- [ ] Define types in `src/types/index.ts`:
  - `FinancialProfile` (income, pay frequency, fixed obligations)
  - `BudgetCategory` (name, monthly limit, current spend)
  - `SavingsGoal` (name, target amount, target date, current amount)
  - `UpcomingExpense` (name, amount, due date, recurring: boolean)
  - `PurchaseVerdict` (verdict: 'yes' | 'maybe' | 'no', reason: string, impact: VerdictImpact)
- [ ] Write unit tests for `canIBuyThis(amount, profile)` engine (all verdict paths)
- [ ] Implement `canIBuyThis()` in `src/utils/budgetEngine.ts`:
  - Calculate discretionary budget remaining this period
  - Calculate spending velocity (actual vs. pace)
  - Check if purchase would delay any savings goal by >1 month
  - Check if purchase + upcoming obligations exceed available cash
  - Return verdict + plain-English reason
- [ ] Write unit tests for `getSpendingVelocity(category, daysIntoMonth)`
- [ ] Implement `getSpendingVelocity()` in `src/utils/budgetEngine.ts`
- [ ] Write unit tests for `getDaysUntilObligations(upcoming[])`
- [ ] Implement `getDaysUntilObligations()`
- [ ] Write unit tests for edge cases: zero income, over-budget already, same-day obligation

**Evidence gate**: All utils tests passing, 100% coverage on `budgetEngine.ts` ✅

---

## Phase 2: State & Persistence ⬜

- [ ] Install Zustand: `npm install zustand`
- [ ] Write tests for store slices (initial state, actions)
- [ ] Implement `src/store/profileStore.ts` — FinancialProfile CRUD
- [ ] Implement `src/store/spendingStore.ts` — BudgetCategories + current period spending
- [ ] Implement `src/store/goalsStore.ts` — SavingsGoals CRUD
- [ ] Implement `src/store/upcomingStore.ts` — UpcomingExpenses CRUD
- [ ] Add localStorage persistence middleware to all stores
- [ ] Write integration test: store round-trips to/from localStorage correctly
- [ ] Handle missing/corrupt localStorage data gracefully (default to empty state)

**Evidence gate**: Store tests passing, manual test of browser reload preserving data ✅

---

## Phase 3: Core UI — The Question Screen ⬜

This is the primary interaction: enter a price, get a verdict.

- [ ] Write component tests for `<VerdictCard>` (yes/maybe/no renders correctly)
- [ ] Build `<VerdictCard verdict={} reason={} impact={} />` component
- [ ] Write component tests for `<AmountInput>` (number validation, submit on enter)
- [ ] Build `<AmountInput onSubmit={} />` component
- [ ] Write component tests for `<QuestionScreen>` (full flow: input → verdict)
- [ ] Build `<QuestionScreen />` — wires `canIBuyThis()` to UI
- [ ] Style with Tailwind: clear yes=green, maybe=yellow, no=red verdict colors
- [ ] Add loading state for calculation (even if instant — good UX habit)
- [ ] Manual test on mobile viewport (375px wide)

**Evidence gate**: Component tests passing, manual test on mobile viewport ✅

---

## Phase 4: Setup & Profile Screens ⬜

- [ ] Write tests for `<IncomeSetup>` form validation
- [ ] Build `<IncomeSetup />` — income, pay frequency, fixed obligations
- [ ] Write tests for `<BudgetSetup>` — add/edit/delete categories
- [ ] Build `<BudgetSetup />` — budget categories with monthly limits
- [ ] Write tests for `<GoalsSetup>` — savings goals management
- [ ] Build `<GoalsSetup />` — goals with target amount + date
- [ ] Write tests for `<UpcomingSetup>` — upcoming expenses list
- [ ] Build `<UpcomingSetup />` — upcoming expenses (one-time + recurring)
- [ ] Add onboarding flow: first-time users land on setup, not question screen
- [ ] Write e2e-style integration test: complete setup → ask question → get verdict

**Evidence gate**: All setup form tests passing, onboarding flow manual test ✅

---

## Phase 5: Dashboard & Spending Tracker ⬜

- [ ] Write tests for `<SpendingVelocityBar>` (on-pace, ahead, behind)
- [ ] Build `<SpendingVelocityBar category={} />` component
- [ ] Write tests for `<SavingsProgressRing>` (% complete)
- [ ] Build `<SavingsProgressRing goal={} />` component
- [ ] Build `<Dashboard />` — spending velocity per category + savings goal progress
- [ ] Add quick-add spending entry from question screen ("I bought it — log it")
- [ ] Write tests for log-purchase flow (verdict + log in one action)

**Evidence gate**: Dashboard renders correctly with real data, log-purchase flow tested ✅

---

## Phase 6: Polish & Harden ⬜

- [ ] Add data export (JSON download of full financial profile)
- [ ] Add data import (restore from JSON backup)
- [ ] Test import/export round-trip
- [ ] Add "reset all data" with confirmation
- [ ] Accessibility audit: keyboard navigation, ARIA labels on verdict cards
- [ ] Run Lighthouse — score ≥ 90 on Performance, Accessibility, Best Practices
- [ ] Write error boundary component + test
- [ ] Final pass: remove all console.log, TODO comments from src/

**Evidence gate**: Lighthouse score screenshot, accessibility audit checklist ✅

---

## Phase 7: Ship ⬜

- [ ] Build production bundle: `npm run build`
- [ ] Deploy to Vercel or GitHub Pages (zero-config)
- [ ] Verify build works on real device (iOS Safari, Android Chrome)
- [ ] Update README with live URL and final screenshot
- [ ] Tag v0.1.0 release

---

## Parking Lot 🅿️

Ideas that are not in scope yet but worth keeping:

- Dark mode toggle
- Multiple profiles (family members)
- Recurring transaction auto-detection
- "What if I skipped X?" simulation mode
- Widget / home screen shortcut (PWA)
- Plaid integration (optional, privacy-first)
- AI-generated spending insights (summary of week)

---

## Lessons Learned 📝

_Update this section as you discover non-obvious things about the project._

| Date | Lesson |
|------|--------|
| — | — |
