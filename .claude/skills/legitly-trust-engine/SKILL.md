# Skill: Legit.ly Trust Engine

> Use this skill when working on shortlinks, attribution, commissions, ledger, payouts, or fraud detection.

## Overview

The trust engine is the most critical system in Legit.ly. It handles the full lifecycle
from click → attribution → commission → payout, with fraud detection and dispute
resolution at every stage.

## Shortlink System

### Generation Rules
- Format: `go.legit.ly/{code}`
- Codes generated with `nanoid` (URL-safe, lowercase, 8-12 chars)
- One tracking_link per creator-product-offer combination
- Links are reusable — do not generate duplicates for the same tuple
- Links can be deactivated but never deleted

### Redirect Flow (Must Complete in <100ms)
1. Request arrives at `/r/{code}`
2. Look up `tracking_link` by `short_code`
3. If not found or inactive → return 404 page
4. Record `click_event` (async if possible to avoid blocking redirect)
5. Set attribution cookie (7-day expiry)
6. Return 302 redirect to destination URL

### Click Event Data
- timestamp, short_code, creator_id, offer_id, product_id
- user_agent_hash, ip_hash (never store raw IP)
- referrer, session_key
- is_bot flag (basic user-agent detection)

### Click Deduplication
- Fingerprint = hash(ip_hash + ua_hash + short_code + 1-minute-window)
- Skip recording if fingerprint exists within window

## Attribution Algorithm

### Primary: Last-Click Attribution
1. When an order arrives via Shopify webhook
2. Look for a click_event matching the customer's session within 7 days
3. If multiple clicks, use the LAST one (most recent)
4. Create attribution record linking order → creator → offer

### Fallback: Coupon Code Attribution
1. If no click match found
2. Check if the order used a coupon code
3. Match coupon against creator-linked coupon codes
4. If match, attribute to that creator

### No Match
- If neither method finds a creator, mark order as unattributed
- Unattributed orders do not generate commissions

## Commission Lifecycle

```
ORDER ARRIVES
    ↓
ATTRIBUTION (click or coupon match)
    ↓
COMMISSION CREATED → state: on_hold
    ↓ (hold period expires, no refund)
COMMISSION RELEASED → state: payable
    ↓ (creator requests, admin approves)
PAYOUT PROCESSED → state: paid
```

### Reversal Paths
- Refund during hold → state: reversed (full reversal)
- Partial refund during hold → state: adjusted (proportional reduction)
- Refund after hold → state: adjusted (create reversal ledger entry)

## Ledger Rules (NEVER VIOLATE)

1. Ledger entries are **INSERT-ONLY** — never UPDATE, never DELETE
2. Every entry has: timestamp, type, amount, wallet_id, reference_id, description
3. Reversals create NEW entries with negative amounts
4. Wallet balances are DERIVED by summing all ledger entries for that wallet
5. Cached balance fields exist for display performance only
6. If cached balance diverges from derived balance → alert, do not auto-correct

## Fraud Detection Rules

| Rule | Trigger | Action |
|------|---------|--------|
| Self-purchase | Order email matches creator email | Create fraud_flag, hold commission |
| Coupon leakage | Coupon used without any click attribution | Create fraud_flag, review |
| Bot spike | >100 clicks from same IP in 1 hour | Create fraud_flag, filter clicks |
| Velocity anomaly | Order value 10x above average | Create fraud_flag, manual review |

## Payout Readiness Checks

Before processing a payout:
1. Creator has Stripe Connect account in good standing
2. Requested amount ≤ available wallet balance
3. No pending fraud flags on the creator
4. No active disputes on included commissions
5. All included commissions are in `payable` state

## Code Guidelines for Trust Engine

- **Always use database transactions** for multi-table operations
- **Always verify idempotency** — webhook handlers must handle re-delivery
- **Never mutate historical records** — append new records for changes
- **Always hash PII** (IP addresses, user agents) before storage
- **Always verify webhook signatures** before processing
- **Log extensively** — every attribution decision, every fraud check
