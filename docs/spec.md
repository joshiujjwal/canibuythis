# CanIBuyThis — Feature Specification

**Version**: 0.1 (pre-implementation)
**Last Updated**: 2025

---

## Problem Statement

Most people don't have a fast, honest answer to "can I afford this right now?" Banking apps show balances — not financial health. Budgeting apps require too much setup and constant maintenance. CanIBuyThis bridges the gap: minimal setup, immediate honest answer.

The primary question to answer: **"If I spend $X today, will I regret it based on my actual financial picture?"**

---

## Goals

- Answer the affordability question in under 5 seconds
- Require no bank account linking — user enters their own data
- Work offline, no server, no accounts
- Persist data in browser localStorage
- Be honest: a "no" answer should feel useful, not punishing

## Non-Goals (v0.1)

- Not a full budgeting app (no receipts, no categories auto-imported)
- Not a bank sync tool
- Not a multi-device sync solution
- Not a tax or investment calculator

---

## Functional Requirements

### Core Verdict Engine

- [ ] **FR-01**: Given a purchase amount and a FinancialProfile, compute a `PurchaseVerdict`
- [ ] **FR-02**: Verdict is one of: `yes`, `maybe`, `no`
- [ ] **FR-03**: Verdict includes a plain-English `reason` (≤ 2 sentences)
- [ ] **FR-04**: Verdict includes a `VerdictImpact` object showing numeric impact on key metrics
- [ ] **FR-05**: Engine considers discretionary budget remaining in current pay period
- [ ] **FR-06**: Engine considers spending velocity (if user is already over pace, lower threshold)
- [ ] **FR-07**: Engine considers upcoming obligations within next 14 days
- [ ] **FR-08**: Engine considers savings goals — purchase must not delay goal by more than 30 days
- [ ] **FR-09**: Edge case: if income is 0 or unset, return `no` with setup prompt reason

### Profile Setup

- [ ] **FR-10**: User can enter monthly (or per-paycheck) take-home income
- [ ] **FR-11**: User can enter fixed monthly obligations (rent, loan, subscriptions)
- [ ] **FR-12**: User can enter budget categories with monthly spending limits
- [ ] **FR-13**: User can log actual spending against categories
- [ ] **FR-14**: User can add savings goals with a target amount and target date

### Upcoming Expenses

- [ ] **FR-15**: User can add one-time upcoming expenses with a due date
- [ ] **FR-16**: User can add recurring expenses (monthly/weekly) with a next-due date
- [ ] **FR-17**: Upcoming expenses within 14 days reduce available discretionary cash

### Persistence

- [ ] **FR-18**: All user data persists in localStorage
- [ ] **FR-19**: Data survives browser refresh and tab close
- [ ] **FR-20**: User can export all data as a JSON file
- [ ] **FR-21**: User can import a previously exported JSON file
- [ ] **FR-22**: Corrupt/missing localStorage data gracefully defaults to empty state (no crash)

---

## Non-Functional Requirements

- [ ] **NFR-01**: Initial page load < 2s on 4G
- [ ] **NFR-02**: Verdict computed in < 100ms
- [ ] **NFR-03**: No data leaves the device (no network calls after initial load)
- [ ] **NFR-04**: Works on Chrome, Safari, Firefox (latest 2 versions)
- [ ] **NFR-05**: Responsive: usable on 375px mobile width
- [ ] **NFR-06**: Accessible: keyboard navigable, ARIA labels on interactive elements
- [ ] **NFR-07**: Lighthouse accessibility score ≥ 90

---

## Data Models

```typescript
// The user's financial baseline
interface FinancialProfile {
  id: string;
  monthlyTakeHomeIncome: number;          // After tax, post-deductions
  payFrequency: 'weekly' | 'biweekly' | 'semimonthly' | 'monthly';
  fixedMonthlyObligations: number;        // Rent, loan minimums, subscriptions
  discretionaryBuffer: number;            // % of discretionary to keep as buffer (default 10)
}

// A spending category (e.g. "Groceries", "Dining", "Entertainment")
interface BudgetCategory {
  id: string;
  name: string;
  monthlyLimit: number;
  currentPeriodSpend: number;             // Reset each pay period
  color?: string;                         // UI display
}

// A savings goal (e.g. "Emergency Fund", "Vacation")
interface SavingsGoal {
  id: string;
  name: string;
  targetAmount: number;
  currentAmount: number;
  targetDate: string;                     // ISO date string
  monthlyContribution: number;            // How much user plans to save per month
}

// An upcoming expense the user has flagged
interface UpcomingExpense {
  id: string;
  name: string;
  amount: number;
  dueDate: string;                        // ISO date string
  recurring: boolean;
  recurrenceInterval?: 'weekly' | 'monthly';
}

// The result of the canIBuyThis() calculation
interface PurchaseVerdict {
  verdict: 'yes' | 'maybe' | 'no';
  reason: string;                         // Plain English, ≤ 2 sentences
  impact: VerdictImpact;
}

interface VerdictImpact {
  discretionaryRemainingAfter: number;    // What's left after purchase
  savingsGoalDelayDays: number;           // How many days this delays the biggest goal
  upcomingObligationsAtRisk: string[];    // Names of obligations that could be tight
  velocityStatus: 'on-pace' | 'ahead' | 'behind'; // Current spending velocity
}
```

---

## Verdict Algorithm (v0.1)

```
canIBuyThis(amount, profile):
  1. Calculate discretionaryBudget = income - fixedObligations - sum(savingsContributions)
  2. Calculate currentPeriodDiscretionary = prorated(discretionaryBudget, payFrequency)
  3. Calculate alreadySpent = sum(currentPeriodSpend across categories)
  4. Calculate upcomingCash = sum(upcomingExpenses due in next 14 days)
  5. Calculate availableNow = currentPeriodDiscretionary - alreadySpent - upcomingCash - buffer%
  
  6. if availableNow <= 0: return NO ("You've already hit your discretionary limit for this period.")
  7. if amount > availableNow: return NO ("This would exceed your available budget by $X.")
  8. if velocityStatus == 'behind' AND amount > availableNow * 0.5: return MAYBE
  9. if any savingsGoal would be delayed > 30 days: return MAYBE
  10. else: return YES
```

---

## Interface Design

### Screen 1: Question Screen (primary)
```
┌─────────────────────────────┐
│  Can I buy this?            │
│                             │
│  $ [____________] [Ask]     │
│                             │
│  ┌──────────────────────┐   │
│  │  ✅ YES              │   │
│  │  You have $234 left  │   │
│  │  this week.          │   │
│  └──────────────────────┘   │
│                             │
│  [Log this purchase]        │
└─────────────────────────────┘
```

### Screen 2: Setup (profile onboarding)

Tab-based: Income | Budget | Goals | Upcoming

### Screen 3: Dashboard

Spending velocity bars + savings goal progress rings

---

## Test Plan

### Unit Tests (`tests/utils/`)

| Test | Input | Expected |
|------|-------|----------|
| Yes verdict | $20, $500 available, no upcoming | yes |
| No verdict — over budget | $300, $50 available | no |
| No verdict — upcoming obligation | $150, $200 available, $180 due in 5 days | no |
| Maybe — behind velocity | $50, $100 available but 80% spent by day 10 | maybe |
| Maybe — goal delay | purchase delays goal by 35 days | maybe |
| Edge: zero income | any amount | no + setup prompt |
| Edge: amount = 0 | $0 | yes (vacuous) |
| Edge: corrupt localStorage | — | empty state, no crash |

### Component Tests (`tests/components/`)

- VerdictCard renders yes/maybe/no variants correctly
- AmountInput validates: rejects non-numeric, negative, empty
- AmountInput submits on Enter key
- QuestionScreen shows verdict after submitting amount
- Dashboard renders with empty state (no crash)
- Setup forms persist to store on submit

### Integration Tests

- Complete onboarding → ask question → see verdict
- Log purchase → re-ask same amount → verdict changes
- Export → clear data → import → data restored

---

## Open Questions

1. **What's the right "buffer %"?** Default 10% feels right but should it be configurable?
2. **Pay period proration**: weekly income vs. monthly budget — how to normalize cleanly?
3. **First-time UX**: should we pre-populate example data so the app feels useful on first load?
4. **Recurring expense handling**: should we auto-advance the due date after logging payment?
5. **Category assignment**: when the user logs a purchase, do they assign a category?
