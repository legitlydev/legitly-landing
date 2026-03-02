# Skill: Legit.ly Product

> Use this skill when making product decisions, creating new features, or evaluating scope.

## Product Identity

Legit.ly is the trust layer for creator commerce. It connects brands with creators
through transparent affiliate relationships where every click, order, and commission
is verifiable.

## Core User Personas

### Creator
- Builds a public profile page at `legit.ly/@handle`
- Curates products from the marketplace onto their storefront
- Shares branded shortlinks (`go.legit.ly/{code}`) to drive sales
- Earns commissions on attributed orders
- Requests payouts when commissions become available

### Brand
- Connects their Shopify store to Legit.ly
- Creates commission-based offers for their products
- Funds their wallet to cover commissions
- Monitors creator performance and attributed revenue
- Approves payout requests

### Admin (Platform)
- Monitors system health, webhook status, and fraud flags
- Reviews and resolves disputes
- Approves payouts
- Manages tenant accounts

## Product Boundaries

### In Scope (MVP)
- Creator profile pages with product storefronts
- Branded shortlink generation and click tracking
- Shopify product import and catalog management
- Commission offers with configurable terms
- Last-click attribution with coupon fallback
- Immutable trust ledger for all financial events
- Wallet funding via Stripe
- Creator payouts via Stripe Connect
- Admin console for oversight and fraud detection

### Out of Scope (Never Build for MVP)
- CRM or contact management
- Native checkout (always redirect to Shopify)
- Social feed, messaging, or DMs
- Multi-platform e-commerce (WooCommerce, BigCommerce, etc.)
- Influencer discovery or matchmaking algorithms
- Content scheduling or social media tools
- Inventory management

## Decision Framework

When facing a product decision:
1. Does it serve the core loop? (Creator curates → Shopper clicks → Brand sells → Creator earns)
2. Does it maintain trust and transparency?
3. Is it Shopify-first?
4. Can it ship within the current prompt scope?
5. Does it avoid scope creep into CRM/social territory?

If any answer is "no," do not build it.
