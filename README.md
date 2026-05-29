# CanIBuyThis 🛒💸

> **Can I buy this today?** A financial clarity tool that answers the question honestly — based on your real budget goals, spending velocity, and upcoming obligations.

![Status](https://img.shields.io/badge/status-🚧%20Early%20Development-orange)
![Stack](https://img.shields.io/badge/stack-React%20%2B%20TypeScript-blue)
![Tests](https://img.shields.io/badge/tests-Vitest-green)

---

## What It Does

You're at the store. You see something you want to buy. Instead of guessing, you open CanIBuyThis, enter the amount, and get a **yes / maybe / no** answer grounded in:

- Your monthly income and fixed obligations
- Your current spending velocity vs. budget targets
- Your savings goals and how on-track you are
- Upcoming expenses you've flagged (rent due, subscription renewals, etc.)

No subscriptions, no bank linking, no data leaving your device.

---

## Tech Stack

| Layer | Choice |
|---|---|
| UI | React 18 + TypeScript |
| Build | Vite |
| Styling | Tailwind CSS |
| State | Zustand + localStorage persistence |
| Tests | Vitest + React Testing Library |
| Linting | ESLint + Prettier |

---

## Getting Started

```bash
# Clone
git clone <repo-url>
cd canibuythis

# Install dependencies
npm install

# Start dev server
npm run dev

# Run tests (always run first!)
npm test

# Lint
npm run lint

# Build for production
npm run build
```

---

## Project Structure

```
canibuythis/
├── src/
│   ├── components/       # React UI components
│   ├── hooks/            # Custom React hooks
│   ├── store/            # Zustand state slices
│   ├── utils/            # Pure calculation logic (budget engine)
│   └── types/            # Shared TypeScript types
├── tests/
│   ├── components/       # Component tests (RTL)
│   ├── hooks/            # Hook tests
│   └── utils/            # Unit tests for budget engine
├── docs/
│   ├── spec.md           # Feature specification
│   └── adr/              # Architecture Decision Records
├── .github/
│   ├── copilot-instructions.md
│   └── instructions/
├── CLAUDE.md
├── AGENTS.md
└── TODO.md
```

---

## Contributing

1. **Read TODO.md first** — pick a task, move it to in-progress
2. **Write the test first** (red phase) — no implementation without a failing test
3. **Implement until tests pass** (green phase)
4. **Review your own diff** before pushing — would you approve this PR?
5. **PRs require evidence**: test output, manual test screenshot or terminal log
6. **Small PRs only** — one feature or fix per PR, no bundled refactors

> Tests are first-class citizens. Deleting a test to make the build pass is a blocking PR issue.
