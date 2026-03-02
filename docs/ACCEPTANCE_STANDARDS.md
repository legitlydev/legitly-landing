# Legit.ly Acceptance Standards

## Every Feature Batch Must

- [ ] App boots with `npm run dev` after changes
- [ ] No TypeScript errors
- [ ] No console errors in browser
- [ ] New routes are protected by appropriate auth/role checks
- [ ] Database migrations run cleanly
- [ ] Acceptance criteria from the prompt are met
- [ ] Key files sent to ChatGPT for review
- [ ] ChatGPT feedback addressed before moving on

## Flow-Specific Standards

### Creator Page Flow
- Creator can sign up, complete profile, pick theme, publish page
- Published page is accessible at /p/{handle}
- Unpublished or nonexistent handles return 404
- Profile requires handle + bio + avatar before publishing
- Handle is unique and URL-safe

### Brand Onboarding Flow
- Brand can sign up, connect Shopify, see imported products
- Shopify OAuth completes without errors
- Catalog sync imports products into local database
- Duplicate sync does not create duplicate products

### Shortlink and Attribution Flow
- Every storefront item has a unique shortlink
- Clicking shortlink records a click event and redirects
- Invalid/inactive shortlinks show 404
- Attribution matches orders to clicks within 7-day window
- Coupon fallback works when no click match exists
- Unattributable orders are correctly left without commission

### Financial Flow
- Commissions calculate correctly from offer terms
- Hold periods work (commission stays on_hold until expiry)
- Refunds reverse or reduce commissions
- Ledger entries are never updated or deleted
- Wallet balances match ledger sums
- Payout requests validate against available balance
- Duplicate payout submissions are prevented

### Security
- No tenant data leakage (brand A cannot see brand B's data)
- Auth middleware on all protected routes
- Webhook signatures verified
- No secrets in committed code
- Admin routes require admin role

## Release Blockers (Any of These = No Deploy)
- Incorrect attribution logic
- Incorrect payout or ledger math
- Broken refund reversal
- Permission/tenant leaks
- Public route failures (creator pages, shortlinks)
- Unverified webhook handlers
- Missing environment variables
