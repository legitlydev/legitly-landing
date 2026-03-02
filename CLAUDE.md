# CLAUDE.md — Legit.ly Project Instructions

> This file is the permanent instruction set for Claude Code on this project.
> Read this file at the start of every session.

---

## Project Identity

**Product**: Legit.ly — The trust layer for creator commerce
**Domain**: legit.ly (main app), go.legit.ly (branded shortlinks)
**Repo**: legitlydev/legitly-landing
**Stage**: Pre-MVP — bootstrapping from a fresh Next.js starter template

---

## What Legit.ly Is

Legit.ly is an affiliate marketing platform that connects **brands** and **creators** through transparent, trust-verified commerce.

- **Creators** build public profile pages at `legit.ly/@handle` showcasing curated products
- **Brands** connect their Shopify stores and create commission-based offers
- **Shortlinks** at `go.legit.ly/{code}` track clicks with transparent attribution
- A **trust ledger** records every financial event immutably
- **Commissions** are calculated, held, verified, and paid out through Stripe

This is NOT a CRM. This is NOT a social network. The creator page IS the product.

---

## Current Codebase State

### Tech Stack
| Layer | Technology | Version |
|-------|-----------|---------|
| Framework | Next.js (Pages Router) | 15.3.2 |
| UI Library | React | 19.0.0 |
| Styling | Tailwind CSS | 4.x |
| PostCSS | @tailwindcss/postcss | 4.x |
| Linting | ESLint + next/core-web-vitals | 9.x |
| Language | JavaScript (jsconfig.json, `@/*` path alias) | ES2022+ |

### File Structure
```
legitly-landing/
├── CLAUDE.md                  # This file — project instructions
├── .claude/                   # Skills, docs, and project specs
│   ├── skills/                # Reusable skill instruction sets
│   │   ├── legitly-product/SKILL.md
│   │   ├── legitly-trust-engine/SKILL.md
│   │   └── legitly-frontend/SKILL.md
│   └── docs/                  # Architecture and spec documents
│       ├── PROJECT_BRIEF.md
│       ├── GUARDRAILS.md
│       ├── SHORTLINK_SPEC.md
│       └── ACCEPTANCE_STANDARDS.md
├── public/                    # Static assets (favicon, SVGs)
├── src/
│   ├── pages/                 # Next.js Pages Router
│   │   ├── _app.js            # App wrapper, imports globals.css
│   │   ├── _document.js       # HTML document structure
│   │   ├── index.js           # Landing page (default Next.js template)
│   │   └── api/
│   │       └── hello.js       # Placeholder API route
│   └── styles/
│       └── globals.css        # Tailwind imports + CSS custom properties
├── next.config.mjs            # reactStrictMode: true
├── eslint.config.mjs          # Flat config, next/core-web-vitals
├── postcss.config.mjs         # @tailwindcss/postcss plugin
├── jsconfig.json              # Path alias @/ → ./src/
├── package.json
└── .gitignore                 # Standard Next.js gitignore
```

### What Exists Today
- Default Next.js 15 landing page template
- Tailwind CSS v4 with PostCSS integration
- Geist Sans and Geist Mono fonts via `next/font`
- A single placeholder API route (`/api/hello`)
- No database, no auth, no middleware, no environment variables

### What Needs To Be Built (MVP Build Order)
1. Architecture and spec documents (`.claude/docs/`)
2. Project bootstrap (App Router migration, Drizzle ORM, folder structure)
3. Auth and tenancy (NextAuth, organizations, role-based access)
4. Creator profiles and public pages (`/p/[handle]`, `/@[handle]`)
5. Brand onboarding and Shopify connection
6. Offers, marketplace, and creator storefronts
7. Shortlink redirects and click tracking (trust-critical)
8. Shopify webhooks, orders, and attribution engine (trust-critical)
9. Wallet, ledger, and payouts (trust-critical)
10. Admin console and fraud detection
11. Analytics dashboards and CSV exports
12. Test suite, seed data, and deployment

---

## Development Conventions

### Commands
```bash
npm run dev       # Start development server (localhost:3000)
npm run build     # Production build
npm run start     # Start production server
npm run lint      # Run ESLint
```

### Code Style
- **JavaScript** with JSDoc annotations where helpful (not TypeScript yet, but planned for bootstrap phase)
- **Tailwind CSS** for all styling — no CSS modules, no styled-components
- **Path aliases**: Use `@/` to reference `src/` (e.g., `import Foo from '@/components/Foo'`)
- **ESLint**: next/core-web-vitals preset — fix all warnings before committing
- **Formatting**: Follow existing code patterns; consistent indentation and spacing
- **Naming**: camelCase for variables/functions, PascalCase for components, snake_case for database columns

### File Organization (Target Structure)
```
src/
├── app/                    # Next.js App Router (after migration)
│   ├── (marketing)/        # Public marketing pages
│   ├── (auth)/             # Login, signup
│   ├── creator/            # Creator dashboard routes
│   ├── brand/              # Brand dashboard routes
│   ├── admin/              # Admin console routes
│   ├── p/[handle]/         # Public creator pages
│   ├── r/[code]/           # Shortlink redirects
│   ├── api/v1/             # Authenticated API routes
│   └── api/webhooks/       # Webhook handlers
├── components/             # Shared React components
│   ├── ui/                 # Base UI components
│   ├── layouts/            # Layout components
│   └── features/           # Feature-specific components
├── db/
│   └── schema/             # Drizzle ORM schema files
├── lib/                    # Shared utilities
│   ├── auth/               # Auth helpers
│   ├── stripe/             # Stripe client and helpers
│   ├── shopify/            # Shopify API client
│   ├── shortlinks/         # Shortlink generation and tracking
│   └── ledger/             # Financial ledger operations
└── styles/
    └── globals.css         # Global styles and Tailwind config
```

### Git Practices
- Small, frequent commits after each successful feature
- Descriptive commit messages: `feat: add creator profile CRUD`, `fix: shortlink redirect 404`
- Never commit secrets — use `.env.local` only
- Verify `npm run dev` boots successfully before committing

---

## Product Rules — Never Break These

### Core Product Truths
1. **Shopify-first** — MVP supports Shopify stores only. No other e-commerce platforms yet.
2. **Creator page IS the product** — `legit.ly/@handle` is the core experience, not a dashboard
3. **Branded shortlinks** — `go.legit.ly/{code}` for all product links
4. **Trust through transparency** — Every click, order, and commission is auditable
5. **Immutable financial history** — Never mutate ledger entries; use reversals/adjustments
6. **Last-click attribution** — 7-day cookie window, coupon code fallback
7. **Hold periods** — Commissions stay on hold before becoming payable
8. **Tenant isolation** — Brand data is never visible to other brands; creator data is private

### What NOT to Build
- Do NOT build a CRM or contact management system
- Do NOT build native checkout — always redirect to Shopify
- Do NOT build a social feed or messaging system
- Do NOT build multi-platform e-commerce support (Shopify only for MVP)
- Do NOT add features beyond what the current prompt requests

### Security Requirements
- Auth on all protected routes (middleware-enforced)
- Webhook HMAC signature verification on all Shopify/Stripe webhooks
- Idempotent webhook processing (dedup via webhook_events table)
- No secrets in committed files — `.env.local` only
- CSRF protection on state-changing endpoints
- Rate limiting on public endpoints (shortlink redirects, public pages)
- Tenant data isolation enforced at query level

### Financial Integrity Rules
- Ledger entries are **append-only** — never UPDATE or DELETE
- Commission reversals create new reversal records, never mutate originals
- Wallet balances are **derived from ledger entries**, cached for display
- Double-entry style: every debit has a corresponding credit
- No duplicate commission creation for the same order
- Refunds always reverse commissions proportionally

---

## Trust Engine — Critical System

The trust engine handles shortlinks, attribution, commissions, and payouts. These are the most sensitive components.

### Shortlink Flow
1. Creator adds product to storefront → tracking_link created with unique short code
2. Visitor clicks `go.legit.ly/{code}` → validate → record click_event → set attribution cookie → 302 redirect
3. Target: redirect completes in under 100ms

### Attribution Flow
1. Shopify order webhook arrives → verify HMAC → deduplicate
2. Check 1: Match customer session to click_event within 7-day window
3. Check 2: If no click match, check coupon code against creator-linked codes
4. Create attribution record → calculate commission → create commission in "on_hold" state

### Commission Lifecycle
`on_hold` → `payable` (after hold period, no refund) → `paid` (after payout)
`on_hold` → `reversed` (if refund occurs during hold)
`payable` → `adjusted` (if partial refund after hold)

### Fraud Detection
- Self-purchase: order email matches creator email
- Coupon leakage: coupon used without click attribution
- Bot spikes: click volume from one IP exceeds threshold

---

## Environment Variables (Expected)

```bash
# Database
DATABASE_URL=                     # Supabase PostgreSQL connection string

# Auth
NEXTAUTH_SECRET=                  # NextAuth.js secret
NEXTAUTH_URL=                     # App URL (http://localhost:3000 for dev)
GOOGLE_CLIENT_ID=                 # Google OAuth
GOOGLE_CLIENT_SECRET=             # Google OAuth

# Shopify
SHOPIFY_API_KEY=                  # Shopify app API key
SHOPIFY_API_SECRET=               # Shopify app secret

# Stripe
STRIPE_SECRET_KEY=                # Stripe secret key
STRIPE_PUBLISHABLE_KEY=           # Stripe publishable key
STRIPE_WEBHOOK_SECRET=            # Stripe webhook signing secret

# App
NEXT_PUBLIC_APP_URL=              # https://legit.ly
NEXT_PUBLIC_SHORTLINK_DOMAIN=     # https://go.legit.ly
```

---

## Session Start Checklist

At the start of every session:
1. Read this file (`CLAUDE.md`)
2. Read all files in `.claude/skills/` and `.claude/docs/`
3. Run `npm run dev` to verify the app boots
4. Check `git status` for any uncommitted changes
5. Ask what to work on next if not given a specific task

---

## Key Dependencies (Planned for Bootstrap)

| Package | Purpose |
|---------|---------|
| drizzle-orm + drizzle-kit | Database ORM and migrations |
| postgres | PostgreSQL driver |
| next-auth / @auth/core | Authentication |
| stripe | Payment processing and payouts |
| lucide-react | Icon library |
| zod | Schema validation |
| nanoid | Short code generation |

---

## Deployment

- **Hosting**: Vercel (free tier)
- **Database**: Supabase PostgreSQL (free tier)
- **Payments**: Stripe (Connect Express for creator payouts)
- **Email**: Resend (free tier, transactional emails)
- **DNS**: legit.ly → Vercel, go.legit.ly → same Vercel deployment with host-based routing
