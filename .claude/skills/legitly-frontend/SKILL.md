# Skill: Legit.ly Frontend

> Use this skill when building UI components, pages, layouts, and client-side interactions.

## Stack

- **Framework**: Next.js 15 with App Router (target after migration)
- **Styling**: Tailwind CSS v4 — no CSS modules, no styled-components
- **Icons**: Lucide React
- **Fonts**: Geist Sans (body), Geist Mono (code/data)
- **Validation**: Zod schemas shared between client and server

## Design System Principles

### Visual Identity
- Clean, professional, trustworthy aesthetic
- White/light backgrounds with sharp typography
- Accent colors for CTAs and status indicators
- Dark mode support via Tailwind's dark: variant

### Layout Patterns
- **Marketing pages**: Full-width, hero sections, centered content
- **Dashboard pages**: Sidebar nav + main content area
- **Public creator pages**: Themed, mobile-first, card-based product grid
- **Forms**: Single-column, clear labels, inline validation

### Component Organization
```
src/components/
├── ui/              # Base primitives (Button, Input, Card, Badge, etc.)
├── layouts/         # AppShell, DashboardLayout, MarketingLayout
└── features/        # Feature-specific (CreatorCard, OfferBuilder, LedgerTable)
```

## Routing Structure

| Route Pattern | Purpose | Auth Required |
|---------------|---------|---------------|
| `/` | Marketing home | No |
| `/login`, `/signup` | Authentication | No |
| `/creator/*` | Creator dashboard | Creator role |
| `/brand/*` | Brand dashboard | Brand role |
| `/admin/*` | Admin console | Admin role |
| `/p/[handle]` | Public creator page | No (SSR) |
| `/r/[code]` | Shortlink redirect | No |
| `/api/v1/*` | Authenticated API | Varies |
| `/api/webhooks/*` | Webhook handlers | Signature verification |

## Frontend Conventions

### Components
- Functional components only (no class components)
- Props destructured in function signature
- Use `className` prop for style overrides (Tailwind merge pattern)
- Co-locate component-specific types with the component file

### State Management
- Server components by default (Next.js App Router)
- Client components only when interactivity is needed (`'use client'`)
- Form state with controlled inputs and Zod validation
- Server actions for form submissions where appropriate

### Data Fetching
- Server components fetch data directly (no useEffect for initial loads)
- Client-side mutations via API routes
- Loading states with Suspense boundaries
- Error boundaries for graceful failure

### Accessibility
- Semantic HTML elements (nav, main, article, section)
- ARIA labels on interactive elements
- Keyboard navigation support
- Color contrast meets WCAG AA
- Focus management on route transitions

### Performance
- Optimize images with `next/image`
- Lazy load below-fold content
- Minimize client-side JavaScript
- Use static generation where possible (public creator pages)

## Theme System (Creator Pages)

Creator pages support selectable themes stored as JSON configs:

```json
{
  "name": "clean",
  "colors": {
    "background": "#ffffff",
    "text": "#1a1a1a",
    "accent": "#3b82f6",
    "card": "#f8fafc"
  },
  "layout": "grid",
  "borderRadius": "md",
  "fontWeight": "normal"
}
```

Three starter themes: `clean`, `bold`, `minimal`.
Themes drive layout and colors on the public page only — dashboard uses the standard design system.
