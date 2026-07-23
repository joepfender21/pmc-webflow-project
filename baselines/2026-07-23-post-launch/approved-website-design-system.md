# Approved Website Design System

This document records the design approved and published on July 23, 2026. Exact class properties remain available in `raw/webflow/styles.json` and the production CSS snapshot.

## Typography

- Display and headings: Clash Display
- Home H1: Clash Display 500
- Supporting copy and interface text: Satoshi 400 or 500
- Strong interface actions: Satoshi 700
- Utility labels and technical metadata: JetBrains Mono 400 or 500
- Services may use JetBrains Mono 600 where already present in the verified runtime
- Satoshi 700 Italic is reserved for deliberate signature emphasis
- Do not synthesize missing font weights or substitute unrelated fonts

### Approved Home hero

- Text: `Make your business easier to find, understand, and choose.`
- Only `choose.` is gold and italic
- `.hero-h1`: width `100%`, maximum width `780px`
- Desktop size: `clamp(42px, 6.4vw, 92px)`
- Weight: `500`
- Line height: `1.02`
- Tracking: `-0.025em`
- `.pmc-home-hero-wide`: spans grid columns 1 through 3 on desktop
- Tablet reset at 991px: grid column returns to `auto`
- Phone override at 767px: 34px, line height 1.07, tracking `-0.015em`
- Supporting copy remains capped at 530px

## Color system

### Locked core colors

- Ink: `#040D0D`
- Cream: `#F5F0E8`
- Gold: `#D7AF33`

### Approved supporting colors

- Deep surface: `#0A1A18`
- Leaf: `#457340`
- Gold hover: `#E5C044`
- Bright leaf: `#5A9E54`
- Body text token: `#8FA8A6`
- Muted token: `#4A5A58`
- Silver token: `#7D8383`

Cream remains the primary readable text color on dark backgrounds. Muted colors are supporting hierarchy, not a replacement for readable body copy.

## Spacing and shape

The verified Webflow variables include:

- Section padding: 144px, 112px, 96px, and 40px
- Button padding: 14px vertical and 28px horizontal
- Gaps: 16px, 24px, 32px, and 48px
- Radii: 6px, 12px, 16px, and pill `999px`

Use existing variables and verified class rules before inventing one-off values.

## Components

### Global navigation

- Webflow component: `PMC Global Navigation`
- Component ID: `5efeb427-7e7f-5e0e-88cc-075a757b63cf`
- Fixed positioning with transparent-to-surface transition behavior
- Desktop navigation contains Home, Services, About, Blog, phone, and Book a call
- Mobile navigation uses the verified hamburger and modal drawer behavior

### Buttons

- Base: `.btn`
- Primary: `.btn.btn-gold`
- Pill shape, 15px Satoshi 700, 14px by 24px padding in the current class
- Gold primary uses `#D7AF33` with dark `#15110A` text
- Hover uses a one-pixel lift, brighter gold, and restrained shadow

### Forms

- Home and Services use Tree CRM public-form embeds
- Embedded public forms are capped at 780px by Tree CRM
- One-option consent renders as a native checkbox
- Genuine multi-option checkbox groups remain pill selectors
- Standalone Tree CRM public forms remain capped at 680px

### Booking

- Book a call opens the verified in-page overlay from Website triggers
- `/meeting` redirects to the Tree CRM discovery-call scheduler
- Overlay behavior preserves Escape close, focus return, scroll lock, reduced motion, and origin-checked height messages

### Audit CTA

The Audit CTA is not an approved active Website component in this baseline. Hidden preserved source remains in Webflow for evidence. Its separate thread owns any future repair or reintroduction.

## Icons

- Preserve the current restrained line-icon language
- Use existing inline SVG or CSS masks where present
- Phone treatment uses the verified CSS mask
- Checkmarks use the approved gold or green state treatment
- Do not replace current icons with emoji
- Keep icon stroke weight, container radius, and color consistent within a component family

## Motion

- Default easing: `cubic-bezier(.16, 1, .3, 1)`
- Typical interface transitions: 200ms to 450ms
- Unified section reveal: fade up 26px over about 800ms with restrained sibling staggering
- Card hover: small lift, border or glow change, and no dramatic scaling
- Continuous motion is limited to deliberate ambient or system-status treatments
- `prefers-reduced-motion: reduce` must disable animation and collapse transition duration
- New motion must support meaning, hierarchy, feedback, or orientation

## Responsive rules

### Webflow breakpoints

- Desktop: base
- Tablet: 991px and below
- Mobile large: 767px and below
- Mobile: 479px and below

### Release verification widths

- 1440px
- 1280px
- 768px
- 390px
- 320px

The release passed without horizontal overflow at all five widths. Future Website changes must be checked at the same widths before publication.

## Accessibility and content rules

- Preserve semantic headings and one H1 per primary page
- Preserve visible focus states
- Preserve `aria-invalid`, `aria-describedby`, modal semantics, focus return, and Escape behavior
- Decorative elements must remain hidden from assistive technology
- Use hyphens instead of em dashes in Website copy
- Preserve reduced-motion behavior
- Do not publish hidden preserved-source elements by removing `hidden` or `aria-hidden` casually

## Change control

This document locks the approved system, not every historical class. Future Website work should:

1. start from this baseline
2. identify the exact Webflow and repository surfaces affected
3. preserve Audit CTA separation unless that lane explicitly authorizes reconciliation
4. verify production behavior at the five release widths
5. capture updated hashes and a new dated baseline after publication
