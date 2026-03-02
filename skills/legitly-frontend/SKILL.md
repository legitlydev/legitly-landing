# Legit.ly Frontend Skill

Use this skill when building UI components, pages, layouts, and creator-facing experiences.

## Design Philosophy

- Creator pages should feel personal and expressive, like a MySpace profile meets a modern storefront
- Brand dashboards should feel clean and operational
- Admin interfaces should be functional, not pretty
- Everything must work well on mobile (mobile-optimized web, no native app)

## Tech Choices

- Next.js App Router with server components by default
- Client components only when interactivity requires it (forms, editors, drag-and-drop)
- Tailwind CSS for all styling
- Lucide React for icons
- Shadcn/ui patterns for form elements, buttons, cards, dialogs
- No external CSS frameworks beyond Tailwind

## Component Organization

```
src/components/
  ui/           → Button, Card, Input, Dialog, Badge, etc.
  layout/       → AppShell, Sidebar, TopNav, Footer
  creator/      → ProfileForm, PageEditor, StorefrontGrid, EarningsCard
  brand/        → OnboardingChecklist, OfferBuilder, CatalogTable, WalletCard
  marketplace/  → OfferCard, ProductCard, FilterBar
  public/       → CreatorHero, ProductGrid, SocialLinks, AffiliateDisclosure
  admin/        → TenantList, FlagQueue, DisputePanel
  shared/       → DataTable, EmptyState, LoadingSpinner, ErrorBoundary
```

## Page Structure Patterns

### Authenticated App Pages (creator/*, brand/*)
- Sidebar navigation (collapsible on mobile)
- Top bar with user menu and org switcher
- Main content area with consistent padding
- Loading states for data-fetching sections
- Empty states with helpful CTAs

### Public Creator Page (/p/[handle])
- Server-rendered for SEO and sharing
- No app shell — standalone page with creator's theme
- Hero section: avatar, banner, display name, bio
- Social links bar
- Product grid organized by collections
- Affiliate disclosure in footer
- OG meta tags for social sharing

### Marketing Pages (/, /for-brands, /for-creators)
- Clean marketing layout
- Split CTAs for brand vs creator signup
- Example creator pages as social proof
- No authenticated nav

## Empty States

Every list, table, and dashboard must have an empty state that:
- Explains what will appear there
- Provides a clear CTA to get started
- Never shows a blank screen or broken layout

## Error Handling

- Form validation errors shown inline next to fields
- API errors shown as toast notifications
- Network errors show a retry option
- 404 pages are branded and helpful
- Never show raw error messages or stack traces to users

## Mobile Responsiveness

- All pages must work on 375px width minimum
- Sidebars collapse to hamburger menu
- Tables become card-based lists
- Forms stack vertically
- Touch targets minimum 44x44px

## Creator Page Themes

MVP includes 3 themes stored as JSON config:
- **Clean**: white background, minimal borders, serif headings
- **Bold**: dark background, bright accent colors, large text
- **Minimal**: light gray, small text, grid-focused

Theme config controls: background color, text color, accent color, heading font, card style, layout density.

## Performance

- Use Next.js Image component for all images
- Lazy load below-fold content
- Keep bundle size minimal — no heavy libraries
- Public creator pages must load in under 2 seconds
- Redirect route must respond in under 100ms
