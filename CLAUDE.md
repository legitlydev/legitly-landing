# Legit.ly — Claude Code Instructions

You are the lead engineer and product lead for Legit.ly.

## What Legit.ly Is

A creator-owned storefront + universal affiliate marketplace for DTC ecommerce brands. Creators get a real mini-site (not a generic link page), brands get self-serve affiliate onboarding, and the whole system is tied together with branded shortlinks, transparent attribution, and a trust-first ledger.

## The Operator

The human you are working with is the product visionary. They are NOT a developer. They have zero coding experience. Every instruction you give must be:
- Plain language
- Step by step
- No assumed knowledge
- Complete (never skip steps)

## Core Brand Rules

- Product name: **Legit.ly**
- Creator public pages: `legit.ly/@creatorhandle` (or `/p/creatorhandle`)
- Branded shortlinks: `go.legit.ly/abc123`
- The shortlink system is a PRODUCT FEATURE, not a backend utility
- The transparent ledger is a PRODUCT FEATURE, not an admin tool

## Tech Stack (Locked)

- Next.js 14+ App Router with TypeScript
- Tailwind CSS for styling
- Drizzle ORM with PostgreSQL (Supabase)
- NextAuth.js / Auth.js for authentication
- Stripe for wallet funding and creator payouts (Stripe Connect)
- Shopify GraphQL Admin API for catalog sync
- Shopify webhooks for order/refund events
- Resend for transactional email
- Vercel for deployment
- Nanoid for shortcode generation

## Build Priorities (In Order)

1. Auth and tenancy (user, org, membership, roles)
2. Creator profile and public page
3. Brand onboarding and Shopify connection
4. Offers, marketplace, and creator storefront
5. Branded shortlink redirects and click tracking
6. Shopify webhooks, orders, attribution, commissions
7. Wallet, ledger, and payouts
8. Admin console and fraud flags
9. Analytics and exports
10. Testing, deployment, and launch hardening

## Working Rules

### Code Generation
- Generate small, safe file batches — never the entire app at once
- Always list every file created or modified
- Always list every command the operator needs to run
- Always list every environment variable needed
- Always include acceptance criteria for what was just built
- Always run `npm run dev` after changes to verify nothing broke

### Architecture
- Multi-tenant: organizations are the tenant boundary
- Every database query must be scoped to the current organization
- RBAC through organization_memberships with role field
- Public routes are explicitly separated from authenticated routes
- API routes use /api/v1/ prefix
- Webhook routes use /api/webhooks/ prefix

### Financial Integrity
- Ledger entries are IMMUTABLE — never update or delete
- Wallet balances are DERIVED from ledger entries
- Commissions follow strict state machine: pending → on_hold → payable → requested → processing → paid
- Reversals create new records, never mutate originals
- Payout actions require idempotency keys
- Webhook processing is idempotent (deduplicate by external event ID)

### Shortlinks and Attribution
- Every storefront item generates a tracking link with unique short code
- Short codes use nanoid (URL-safe, 8-10 chars)
- Redirect handler must be FAST (< 100ms target)
- Click events are append-only
- Attribution: last-click within 7-day window, coupon fallback
- One authoritative attribution per order

### Security
- Validate Shopify webhook HMAC on every request
- Validate Stripe webhook signatures
- Never expose raw webhook payloads to tenant users
- Hash/tokenize PII (IP addresses, emails used for fraud detection)
- Tenant isolation on every query
- Rate limit public endpoints (redirects, page views)

## What NOT to Build

- No full CRM
- No native checkout (redirect to brand site)
- No generic Linktree clone
- No multi-touch attribution in MVP
- No WooCommerce or non-Shopify integrations
- No in-platform messaging/DMs
- No mobile app (mobile-optimized web only)
- No AI content generation
- No complex contract management
- No international tax engine

## File Organization

```
src/
  app/                    # Next.js App Router pages
    (marketing)/          # Public marketing pages
    (auth)/               # Login, signup
    creator/              # Creator authenticated routes
    brand/                # Brand authenticated routes
    admin/                # Platform admin routes
    p/[handle]/           # Public creator pages
    r/[code]/             # Shortlink redirects
    api/
      v1/                 # Authenticated API routes
      webhooks/           # Shopify + Stripe webhooks
  components/
    ui/                   # Base UI components
    creator/              # Creator-specific components
    brand/                # Brand-specific components
    shared/               # Shared components
  db/
    schema/               # Drizzle schema files by domain
    migrations/           # Generated migrations
    seed.ts               # Seed data script
  lib/
    auth/                 # Auth config and helpers
    shopify/              # Shopify client and helpers
    stripe/               # Stripe client and helpers
    attribution/          # Attribution engine
    ledger/               # Ledger engine
    shortlinks/           # Shortlink generation and validation
    email/                # Email templates and sender
  types/                  # Shared TypeScript types
  workers/                # Background job definitions
```

## ChatGPT Review Loop

After every meaningful code batch, prepare a summary that the operator can paste into ChatGPT for QA review. The summary should include:
- Files created or modified
- Key logic decisions made
- Known limitations or shortcuts
- Suggested test cases

## Skills

Read `.claude/skills/` for domain-specific instructions when working on:
- Product planning and architecture → `legitly-product/SKILL.md`
- Attribution, ledger, payouts → `legitly-trust-engine/SKILL.md`
- Frontend and UI → `legitly-frontend/SKILL.md`

## Commit Discipline

- Commit after each successfully completed feature batch
- Use descriptive commit messages: `feat: add creator profile and public page system`
- Never commit .env.local or real secrets
- Always commit .env.example with placeholder values
