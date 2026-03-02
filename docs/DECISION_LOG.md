# Legit.ly Decision Log

Track every important decision made during the build. Update this after each prompt.

## Format

```
### [DATE] — [DECISION TITLE]
**Context:** Why this came up
**Decision:** What we decided
**Alternatives considered:** What else we could have done
**Risks:** What could go wrong
**Status:** Active / Superseded / Under review
```

## Decisions

### Day 1 — Tech Stack
**Context:** Need to choose stack for MVP
**Decision:** Next.js 14+ App Router, TypeScript, Tailwind, Drizzle ORM, PostgreSQL (Supabase), Stripe, NextAuth.js
**Alternatives:** Remix, Prisma, self-hosted Postgres, custom auth
**Risks:** Drizzle ORM is newer than Prisma (less community support); Supabase free tier has limits
**Status:** Active

### Day 1 — URL Structure
**Context:** Need to define branded URL patterns
**Decision:** Creator pages at legit.ly/@handle (or /p/handle), shortlinks at go.legit.ly/code
**Alternatives:** Subdomains per creator, single domain for everything
**Risks:** go.legit.ly requires separate DNS config; @ prefix may conflict with Next.js routing
**Status:** Active

### Day 1 — Attribution Model
**Context:** Need attribution rules for MVP
**Decision:** Last-click, 7-day window, coupon code fallback, one attribution per order
**Alternatives:** Multi-touch, first-click, longer windows
**Risks:** Simple model may miss some legitimate attributions; coupon leakage is a known risk
**Status:** Active

---
*Add new decisions below as the build progresses*
