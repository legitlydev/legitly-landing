# Legit.ly — Project Brief

## One-Line Summary

Legit.ly is the trust layer for creator commerce: a platform where creators curate branded storefronts, share trackable shortlinks, and earn transparent commissions on attributed Shopify sales.

## The Problem

Creator affiliate marketing is broken:
- **Creators** don't trust that they're getting credited for sales they drive
- **Brands** don't trust that affiliate clicks and attributions are legitimate
- **Neither side** has visibility into the full click → order → commission → payout pipeline
- Existing platforms are opaque, complex, and built for enterprise advertisers — not for independent creators

## The Solution

Legit.ly makes every step of the affiliate relationship transparent and verifiable:

1. **Creator Pages** (`legit.ly/@handle`) — Beautiful, themed storefront pages where creators showcase curated products
2. **Branded Shortlinks** (`go.legit.ly/{code}`) — Every product link is trackable with transparent click data
3. **Trust Ledger** — An immutable financial record of every commission, reversal, and payout
4. **Shopify-Native** — Products come directly from the brand's Shopify store; orders are verified via webhooks

## Target Users

### Primary: Independent Creators (50-500K followers)
- Content creators, influencers, bloggers, YouTubers
- Already recommending products to their audience
- Want a simple, beautiful way to monetize recommendations
- Value transparency and trust in their brand partnerships

### Secondary: Direct-to-Consumer Brands
- Shopify-based e-commerce brands
- Want to activate creator partnerships without complex affiliate networks
- Value quality over quantity (curated creator relationships)
- Need transparent ROI tracking

## MVP Scope

### Must Have (Launch Blockers)
- Brand signup → connect Shopify → import catalog → create offer → fund wallet
- Creator signup → build profile page → browse marketplace → add products → publish page
- Shortlink redirect with click tracking and attribution cookie
- Shopify order webhook processing with last-click attribution
- Commission calculation, hold periods, and state machine
- Immutable ledger with derived wallet balances
- Creator payout requests → admin approval → Stripe Connect transfer
- Admin console with fraud flags and dispute resolution
- Public creator pages at `legit.ly/@handle`

### Nice to Have (Post-Launch)
- Analytics dashboards with charts
- CSV exports for earnings and performance
- Multiple theme options for creator pages
- Creator application workflow for restricted offers
- Email notifications (welcome, commission earned, payout processed)

### Out of Scope (Never for MVP)
- Non-Shopify e-commerce platforms
- Native checkout (shoppers buy on Shopify)
- CRM or contact management
- Social features (messaging, feed, comments)
- Content creation tools
- Influencer discovery or matchmaking

## Key Metrics (Post-Launch)

| Metric | Target |
|--------|--------|
| Beta brands onboarded | 10 |
| Beta creators active | 50 |
| Shortlink redirect latency | < 100ms |
| Attribution accuracy | > 95% |
| Ledger reconciliation errors | 0 |
| Payout processing time | < 48 hours |

## Domain Architecture

- `legit.ly` — Main application (marketing, dashboards, auth)
- `legit.ly/@handle` — Public creator pages
- `go.legit.ly/{code}` — Branded shortlink redirects
- `legit.ly/p/{handle}` — Canonical creator page URL (SSR)
- `legit.ly/r/{code}` — Shortlink redirect handler (fallback if subdomain not configured)
