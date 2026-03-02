# Legit.ly Trust Engine Skill

Use this skill when working on: shortlinks, redirects, click tracking, attribution, commissions, refunds, wallets, ledger entries, payouts, fraud detection, disputes, or audit logging.

## This Is the Most Critical Domain

Financial integrity and attribution correctness are the foundation of user trust. Bugs here destroy the product. Apply maximum rigor.

## Shortlink Rules

- Format: `go.legit.ly/{shortcode}`
- Short codes: nanoid, URL-safe, 8-10 characters
- Every storefront_item gets exactly one active tracking_link
- Shortlinks can be deactivated but never deleted
- Redirect must: validate link → record click → set cookie → 302 redirect
- Target latency: under 100ms

## Click Tracking Rules

- click_events are APPEND-ONLY (never update or delete)
- Deduplicate by fingerprint: hash(ip + user_agent + short_code + minute_bucket)
- Record: timestamp, short_code, creator_id, offer_id, product_id, ua_hash, ip_hash, referrer, session_key
- Basic bot detection: filter known bot user agents, flag suspicious patterns

## Attribution Rules

- Model: last-click, 7-day window
- Step 1: Match order customer session to click_event within window
- Step 2: If no click match, check coupon code against creator-linked codes
- Step 3: If neither matches, order is unattributed (no commission)
- ONE attribution per order (no splitting in MVP)
- Attribution is recorded as a separate record linking order → creator → offer
- Attribution uses the offer_version that was active at time of order

## Commission State Machine

```
pending → on_hold → payable → requested → processing → paid
                                                       ↗
pending → on_hold → denied (fraud/policy)
                  → reversed (refund)
payable → reversed (late refund)
```

- NEVER skip states
- NEVER mutate a commission record — create new state transitions
- Hold period is configurable per offer (default: 14 days)
- Hold release is a background job, not a manual action

## Ledger Rules

- ledger_entries are IMMUTABLE — no updates, no deletes, ever
- Every financial event creates ledger entries
- Entry types: funding, commission_earned, commission_reversed, payout_sent, adjustment
- Each entry has: wallet_id, direction (debit/credit), amount, related_object_type, related_object_id
- Wallet balances are ALWAYS derived by summing ledger entries
- Cached balance fields exist for display speed but are recalculated, never manually set
- If cached balance diverges from ledger sum, the LEDGER IS TRUTH

## Refund Handling

- Shopify refund webhook triggers commission review
- If commission is on_hold: reverse it (create reversal entry)
- If commission is payable: reverse it (create reversal entry, update wallet)
- If commission is already paid: create negative adjustment, flag for manual review
- Partial refunds reduce commission proportionally

## Payout Rules

- Creator can request payout of available balance
- Available balance = sum of payable commissions not yet requested
- Minimum payout threshold (configurable, default $25)
- Payout requires completed Stripe Connect onboarding
- Payout request creates payout_request record
- Approval creates payout record and triggers Stripe transfer
- Stripe payout webhook confirms or fails the transfer
- Idempotency key required on every Stripe payout call

## Fraud Detection

Flag these patterns:
- Self-purchase: order email matches creator email or known aliases
- Coupon leakage: coupon used without any click attribution from that creator
- Bot spikes: >100 clicks from single IP in 1 hour
- Conversion anomaly: conversion rate >50% (suspicious for small click volumes)
- Flags create fraud_flag records, do NOT auto-deny commissions
- Human review required for all flag resolutions

## Webhook Processing

- Store every webhook in webhook_events before processing
- Deduplicate by (provider, external_event_id)
- Verify HMAC/signature before any processing
- If a dependency is missing (e.g., product not yet synced), mark event as deferred
- Deferred events retry on a schedule
- Nightly reconciliation job compares local records against Shopify

## Audit Trail

- Every financial action, every approval, every dispute resolution logged
- audit_logs: actor, action, object_type, object_id, before_state, after_state, timestamp
- Admin actions are always audited
- Impersonation is heavily audited
