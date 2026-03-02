# Legit.ly — Guardrails

> Rules that must never be violated during development. Read before every coding session.

---

## Financial Integrity (CRITICAL)

1. **Ledger entries are immutable** — Never UPDATE or DELETE a ledger_entries row. Ever.
2. **Reversals create new records** — To undo a commission, INSERT a reversal entry with negative amount.
3. **Wallet balances are derived** — The source of truth is `SUM(ledger_entries)` for a wallet. Cached balances are for display only.
4. **Double-entry accounting** — Every debit from a brand wallet has a corresponding credit to a creator wallet (or platform wallet).
5. **No orphaned money** — Every dollar that enters the system must be traceable through the ledger.
6. **Commissions respect hold periods** — Never skip the `on_hold` state. Commissions must age before becoming payable.
7. **Refunds always cascade** — A Shopify refund must create a commission reversal and ledger adjustment.

## Data Integrity

8. **Idempotent webhook processing** — Store webhook event IDs. If a webhook_event record exists for that ID, skip processing.
9. **No duplicate commissions** — Check for existing commission before creating one for an order line item.
10. **Attribution is deterministic** — Given the same inputs, the attribution algorithm must always produce the same result.
11. **Click deduplication** — Same visitor + same link + same minute = one click event.
12. **Never store raw PII** — Hash IP addresses and user agents before storage. Never log raw customer data.

## Security

13. **Verify all webhook signatures** — HMAC verification on every Shopify and Stripe webhook. Reject invalid signatures immediately.
14. **Tenant isolation at query level** — Every database query for brand/creator data must include `org_id` in the WHERE clause.
15. **Role-based route protection** — Middleware must enforce: creator routes need creator org, brand routes need brand org, admin routes need admin role.
16. **No secrets in code** — All credentials, API keys, and tokens go in `.env.local`. Never in source files.
17. **Validate all inputs** — Use Zod schemas on all API route inputs. Never trust client data.
18. **Encrypt stored tokens** — Shopify access tokens and Stripe keys stored in the database must be encrypted at rest.

## Code Quality

19. **One feature per prompt** — Do not combine unrelated features in a single implementation pass.
20. **Verify the app boots** — After every change, `npm run dev` must succeed. Never commit broken code.
21. **Run lint before commit** — `npm run lint` must pass with zero warnings.
22. **No dead code** — Remove unused imports, variables, and functions. Don't comment out code "for later."
23. **Test trust-critical paths** — Attribution, commission calculation, ledger operations, and payout logic must have test coverage.

## Scope Control

24. **Shopify only** — Do not add support for WooCommerce, BigCommerce, or any other platform.
25. **No native checkout** — Shoppers buy on Shopify. Legit.ly tracks and attributes, never processes orders.
26. **No CRM features** — No contact lists, email campaigns, audience segmentation, or customer profiles.
27. **No social features** — No messaging, feeds, comments, follows, or notifications beyond transactional emails.
28. **Solve the current prompt** — Do not speculatively build features that haven't been requested yet.

## Performance

29. **Shortlink redirects under 100ms** — The `/r/{code}` handler must be fast. Record clicks asynchronously if needed.
30. **Public pages are SSR/SSG** — Creator pages at `/p/{handle}` should be server-rendered for SEO and performance.
31. **Minimize client JavaScript** — Use server components by default. Add `'use client'` only when interactivity requires it.
