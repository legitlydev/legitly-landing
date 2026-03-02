# Legit.ly Product Orchestrator Skill

Use this skill when planning features, generating architecture docs, creating implementation batches, or making product decisions.

## Priority Stack

1. Creator public page — the primary product surface
2. Branded shortlink system — part of the brand identity
3. Attribution integrity — the trust foundation
4. Ledger transparency — user-visible financial truth
5. Payout trust — creators must believe the system
6. Shopify-first brand onboarding — fast time-to-value

## Feature Batch Rules

When generating a feature batch:
- Scope to ONE domain at a time
- List every file to create or modify
- List every database migration needed
- List every environment variable needed
- List every terminal command the operator must run
- Include acceptance criteria as a checklist
- Include manual test steps in plain language
- Always verify the app still boots after changes

## Architecture Decisions

- Monorepo: everything in one Next.js project
- Database: PostgreSQL via Supabase with Drizzle ORM
- Auth: NextAuth.js with credentials + Google OAuth
- File storage: Supabase Storage
- Background jobs: Vercel Cron or inline async processing for MVP
- Email: Resend
- Payments: Stripe (Connect for creator payouts, Payment Intents for brand funding)

## Build Sequencing

Never build a feature that depends on something not yet built. The dependency chain is:

```
Auth → Creator Profiles → Brand Onboarding → Offers → Marketplace →
Storefront Items → Shortlinks → Click Tracking → Webhooks → 
Attribution → Commissions → Ledger → Payouts → Admin → Analytics
```

## Non-Goals (Enforce Strictly)

- Do NOT add CRM features
- Do NOT build native checkout
- Do NOT add multi-platform support (Shopify only in MVP)
- Do NOT add AI recommendations
- Do NOT add complex messaging
- Do NOT add features that aren't in the build priority list
