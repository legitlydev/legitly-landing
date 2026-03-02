# Legit.ly Branded Shortlink and Attribution Specification

## URL Model
- Creator pages: `legit.ly/@creatorhandle` or `legit.ly/p/creatorhandle`
- Shortlink redirects: `go.legit.ly/abc123`
- API: `legit.ly/api/v1/...`
- Webhooks: `legit.ly/api/webhooks/...`

## Shortlink Data Model
Each shortlink connects: creator → storefront_item → product → offer → brand → destination_url

Fields: id, storefront_item_id, short_code (unique, nanoid 8-10 chars), destination_url, creator_org_id, offer_id, product_id, status (active/inactive/rotated), created_at

## Redirect Flow
1. Request arrives at /r/{code}
2. Look up tracking_link by short_code
3. If not found or inactive → 404 branded error page
4. Generate click fingerprint: hash(ip + ua + code + minute)
5. Check dedupe — skip recording if fingerprint exists
6. Record click_event (append-only)
7. Set/refresh attribution cookie: session_key, creator_id, offer_id, 7-day expiry
8. 302 redirect to destination_url with tracking params
9. Target: entire flow under 100ms

## Click Event Record
- click_id, tracking_link_id, short_code
- creator_org_id, offer_id, product_id
- session_key, click_fingerprint
- ua_hash, ip_hash, referrer
- bot_suspicion_score (0 = clean, 1+ = suspicious)
- occurred_at
- IMMUTABLE — never update or delete

## Attribution Algorithm
When a Shopify order webhook arrives:
1. Extract customer identifier and any discount codes
2. CHECK CLICK PATH: Find most recent click_event where session matches and occurred_at is within 7 days of order
3. If found → create attribution with source_type = "click"
4. CHECK COUPON FALLBACK: If no click match, check if discount code is linked to a creator
5. If found → create attribution with source_type = "coupon_fallback"
6. If neither → order is unattributed, no commission created

## Commission Calculation
- Look up offer_version active at time of order
- commission_amount = order.commissionable_total × offer_version.commission_percent
- Commission created in on_hold state
- hold_expires_at = order.created_at + offer.hold_period_days

## Fraud Signals
- Self-purchase: order.customer_email matches creator email → flag
- Coupon leakage: coupon used but no click from that creator in any window → flag
- Bot spike: >100 clicks from same ip_hash in 60 minutes → flag
- Flags do NOT auto-deny. They queue for review.

## Ledger Integration
Every commission state change creates ledger entries:
- Commission earned → credit creator wallet, debit brand wallet (reserved)
- Commission reversed → reverse the above
- Payout sent → debit creator wallet
- Brand funding → credit brand wallet
