# Legit.ly — Acceptance Standards

> Every feature must meet these standards before it is considered complete.

---

## Universal Standards (Apply to ALL Features)

### Code Quality
- [ ] `npm run dev` starts without errors
- [ ] `npm run build` succeeds without errors
- [ ] `npm run lint` passes with zero warnings
- [ ] No TypeScript/JSDoc type errors
- [ ] No unused imports or variables
- [ ] No hardcoded secrets or credentials
- [ ] No `console.log` statements left in production code

### Security
- [ ] Protected routes require authentication
- [ ] API routes validate input with Zod schemas
- [ ] Tenant data is isolated (org_id in all queries)
- [ ] No XSS vectors (user content is escaped)
- [ ] No SQL injection (parameterized queries via Drizzle ORM)

### UI/UX
- [ ] Responsive on mobile, tablet, and desktop
- [ ] Loading states shown during async operations
- [ ] Error states displayed clearly to the user
- [ ] Forms show inline validation errors
- [ ] Keyboard navigation works for all interactive elements

---

## Feature-Specific Standards

### Auth & Tenancy
- [ ] Signup creates user + organization + membership
- [ ] Login returns valid session/JWT
- [ ] Creator routes blocked for brand users (and vice versa)
- [ ] Admin routes blocked for non-admin users
- [ ] Session persists across page refreshes
- [ ] Logout clears session completely

### Creator Profile & Pages
- [ ] Profile saves as draft without publishing
- [ ] Handle uniqueness enforced (URL-safe, lowercase)
- [ ] Published page renders at `/p/{handle}`
- [ ] Unpublished page returns 404
- [ ] Page has correct meta tags and OG tags for social sharing
- [ ] Theme selection changes the public page appearance
- [ ] Avatar and banner upload works

### Brand Onboarding & Shopify
- [ ] Shopify OAuth flow completes successfully
- [ ] Access token stored encrypted in database
- [ ] Catalog import fetches all products and variants
- [ ] Import progress shown in UI
- [ ] Re-import updates existing products (not duplicates)
- [ ] Disconnecting store cleans up token

### Offers & Marketplace
- [ ] Offer saves as draft, can be activated
- [ ] Commission percentage is validated (0-100%)
- [ ] Creator marketplace shows only active offers
- [ ] "Add to storefront" creates tracking link
- [ ] Storefront ordering persists
- [ ] Removing a product deactivates (not deletes) the tracking link

### Shortlinks & Click Tracking
- [ ] Redirect completes in under 100ms
- [ ] Click event recorded with all required fields
- [ ] Attribution cookie set with correct expiry (7 days)
- [ ] Bot clicks flagged correctly
- [ ] Duplicate clicks within 1-minute window deduplicated
- [ ] Invalid/inactive codes return 404
- [ ] Session key persists across clicks (30-day cookie)

### Attribution & Commissions
- [ ] Order webhook verified via HMAC
- [ ] Duplicate webhooks handled idempotently
- [ ] Last-click attribution matches correct creator
- [ ] Coupon fallback works when no click match
- [ ] Unattributed orders logged but no commission created
- [ ] Commission created in `on_hold` state
- [ ] Commission amount calculated correctly from offer terms
- [ ] Hold period releases commission to `payable` state
- [ ] Refund reverses commission with new ledger entry

### Wallet & Ledger
- [ ] Brand can fund wallet via Stripe
- [ ] Ledger entry created for every financial event
- [ ] Ledger entries are truly immutable (no UPDATE/DELETE)
- [ ] Wallet balance matches sum of ledger entries
- [ ] Creator sees correct available/pending/total balances
- [ ] Payout request validates amount against available balance
- [ ] Payout approval creates ledger debit entry

### Admin & Fraud Detection
- [ ] Admin can view all tenants
- [ ] Fraud flags auto-created for self-purchase, coupon leakage, bot spikes
- [ ] Dispute workflow allows approve/deny/adjust
- [ ] All admin actions logged in audit_logs
- [ ] Webhook status monitor shows recent events

### Analytics
- [ ] Date range picker filters data correctly
- [ ] KPI cards show accurate numbers
- [ ] CSV exports contain correct data
- [ ] Export handles large datasets without timeout

---

## Trust Engine Integration Tests

These scenarios must pass end-to-end:

### Happy Path
1. Creator adds product → tracking link created
2. Visitor clicks link → click recorded → cookie set → redirect works
3. Visitor buys on Shopify → webhook arrives → order recorded
4. Attribution matches click to order → commission created (on_hold)
5. Hold period expires → commission becomes payable
6. Creator requests payout → admin approves → Stripe transfer initiated
7. Ledger shows complete trail: funding → commission → payout

### Refund Path
1. Order attributed and commission on_hold
2. Refund webhook arrives
3. Commission reversed with ledger adjustment
4. Creator wallet balance reduced

### Fraud Path
1. Creator purchases through their own link
2. Fraud flag auto-created (self-purchase)
3. Commission held pending review
4. Admin resolves dispute

### No Attribution Path
1. Order arrives with no matching click or coupon
2. Order marked as unattributed
3. No commission created
4. Brand dashboard shows unattributed order
