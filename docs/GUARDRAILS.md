# Legit.ly Guardrails and Non-Goals

## Core Guardrails
1. The creator page is the product — never reduce it to an afterthought
2. Branded shortlinks (go.legit.ly) are a product feature, not plumbing
3. The ledger is transparent and immutable — never mutate financial history
4. Attribution logic must be explainable and auditable
5. Shopify-first beats universal-but-shallow
6. Small batches, one feature at a time
7. Every financial action must be idempotent
8. Every webhook must be replayable
9. No code deploys without review

## Non-Goals for MVP
- Full CRM
- Native ecommerce checkout
- Multi-touch attribution
- WooCommerce or non-Shopify integrations
- International tax engine
- In-platform messaging or DMs
- Mobile native app
- AI content generation
- Complex contract management
- Enterprise hierarchy
- Advanced analytics or BI tools

## UX Guardrails
- Every flow must be understandable by a non-technical user
- Creator page publishing must feel simple
- Brand onboarding must feel achievable in under 30 minutes
- Wallet and earnings states must use plain language
- Errors must explain what happened and what to do next

## Technical Guardrails
- All database queries scoped to organization
- No direct SQL unless Drizzle ORM cannot express the query
- Env vars for all external service credentials
- Webhook signature verification on every handler
- Rate limiting on all public endpoints
- Never expose internal IDs to external users where avoidable
