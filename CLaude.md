# PMC Webflow Project - Claude Code Reference

## What This Project Is

This repo contains all custom code for **pfendermarketing.com**, built in Webflow. The code lives here as the source of truth and gets copy-pasted into Webflow's code slots. This is NOT a traditional dev project - there is no build step, no bundler, no deployment pipeline. The workflow is: edit code here → copy into Webflow → publish.

## The Three Code Layers (Critical - Read This First)

Webflow has three places where custom code lives. Every code change must go in the RIGHT layer or things break.

### Layer 1: Global Site Settings
- **Files:** `global/head.html` and `global/footer.html`
- **Where in Webflow:** Dashboard > Site Settings > Custom Code > Header / Footer
- **Scope:** Runs on EVERY page of the site
- **Rule:** Keep this minimal. Only truly site-wide code goes here.
- **Currently contains:** Just smooth scrolling CSS and password page placeholder styling

### Layer 2: Page Settings (Page-Specific)
- **Files:** `pages/{page-name}/head.html` and `pages/{page-name}/body.html`
- **Where in Webflow:** Designer > Pages panel > [Page] > Settings (gear icon) > Custom Code
- **head.html** = Inside `<head>` tag. Contains ALL CSS for that page. Wrapped in `<style>` tags.
- **body.html** = Before `</body>` tag. Contains ALL JavaScript for that page. Wrapped in `<script>` tags.
- **Rule:** ALL CSS goes in page head. ALL JS goes in page body. Never in embeds.

### Layer 3: Embed Elements (On the Canvas)
- **Files:** `pages/{page-name}/embeds/*.html`
- **Where in Webflow:** Inside Div Blocks on the Webflow canvas (visible in Navigator panel)
- **Rule:** HTML ONLY. No `<style>` tags. No `<script>` tags. Ever.
- **Why:** Webflow's Designer re-renders embeds constantly. CSS/JS in embeds causes crashes, duplicate event listeners, and layout thrashing. This was the root cause of past crashes.

## Brand Tokens (Use These Exact Values)

### Colors
- Background (primary dark): `#040D0D`
- Leaf Green: `#457340`
- Gold: `#D7AF33`
- Off-white (primary text): `#F5F0E8`
- Deep secondary bg: `#0A1A18`

### Text Opacity Tiers
- Primary text: `#F5F0E8`
- Body paragraphs: `rgba(245,240,232,0.7)` or `0.8`
- Subtext/supporting: `rgba(245,240,232,0.6)`
- Footnotes/attribution: `rgba(245,240,232,0.5)`
- Labels/placeholder: `rgba(245,240,232,0.35)`

### Typography
- Headings: `'Clash Display', sans-serif`
- Body/UI: `'Satoshi', sans-serif`
- H1: Clash Display 700, 60px, -0.03em, line-height 1.0
- H2: Clash Display 600, 38px, -0.02em, line-height 1.1
- H3: Clash Display 500, 26px, -0.01em, line-height 1.2
- Labels: Satoshi 500, 12px, 0.14em tracking, ALL CAPS, Gold
- Body: Satoshi 400, 17px, line-height 1.75

### Spacing Scale (Only Use These)
4, 8, 12, 16, 24, 32, 48, 64, 80, 96, 128 (base unit 4px)

## Layout System

### Two-Width Rule
- **Container width (wide):** max 1040px - for modules, cards, embeds, dashboards
- **Editorial width (narrow):** max 680-760px - for reading sections, intros, manifestos
- Editorial blocks are LEFT-ALIGNED (margin-left: 0, margin-right: auto), not centered
- Hero section is the ONLY section that can be centered

### Vertical Rhythm
- `cs-page-stack` controls vertical spacing: flex column with gap
- Desktop: gap 80px / Tablet: gap 64px / Mobile: gap 48px
- Do not add random large margins. Use the stack gap.

## Class Naming Contracts (Scripts Depend On These)

These class names are referenced by JavaScript. If renamed in Webflow Navigator, the page head/body code MUST be updated to match.

### Shared across pages:
- `.section-primary-wrapper` - hero wrapper (spotlight + aurora target)
- `.start-logo-stage` - logo badge (halo ring target)

### /start page:
- `.pmc-start` - form card wrapper (shimmer target)
- `.h1-start`, `.subtext-start`
- Shimmer class: `.pmc-shimmer-active`
- CSS custom properties: `--pmc-x`, `--pmc-y`, `--pmc-s`

### /meeting page:
- `.pmc-scheduler-card` - scheduler card wrapper (shimmer target)
- `.meeting-pmc-hero-sub`
- Shimmer class: `.pmc-shimmer-active`
- CSS custom properties: `--pmc-x`, `--pmc-y`, `--pmc-s`

### /case-studies page:
- `.section-primary-wrapper.section-cs` - hero wrapper (combo class scoping)
- `.start-logo-stage.is-cs` - logo badge (combo class scoping)
- `.div-embed-divider.is-cs` - divider elements
- `.pmc-cs-action` - form card wrapper (shimmer target)
- Shimmer class: `.cs-shimmer-active`
- CSS custom properties: `--cs-x`, `--cs-y`, `--cs-s`
- All prefixed classes: `pmc-csa-*`, `pmc-fcb-*`, `pmc-showcase-*`, `pmc-results-*`, `pmc-phone-*`, `pmc-logo-*`, `pmc-metric-*`

## Combo Classes (How Page Scoping Works)

Webflow combo classes prevent styles from bleeding between pages. The same base class gets a different modifier per page:

- `/start`: `.section-primary-wrapper` (no modifier, uses `--pmc-*` vars)
- `/meeting`: `.section-primary-wrapper` (no modifier, uses `--pmc-*` vars)
- `/case-studies`: `.section-primary-wrapper.section-cs` (uses `--cs-*` vars)

## JavaScript Safety Patterns (Always Include These)

Every page body script MUST include:
1. **Run-once guard:** `if(window.__pmc_{page}_page)return; window.__pmc_{page}_page=true;`
2. **Designer detection:** `try{if(window.Webflow&&Webflow.env&&Webflow.env('design'))return;}catch(e){}`
3. **Throttled event handlers:** Use `requestAnimationFrame` gating for mousemove/scroll
4. **Cache getBoundingClientRect():** Only refresh on resize/scroll, not every mouse event

## Form Integration

All forms POST JSON to Make.com webhook:
- Endpoint: `https://hook.us2.make.com/i4ig2ne5wf2e1dwqisjiblwbed6rcyvt`
- Each form includes a `source` field to identify which page it came from
- Validation: first_name, last_name, email, phone, at least one service, budget, referral
- Success state: hide form, show animated checkmark + confirmation message

## Responsive Breakpoints
- Tablet: 768px
- Mobile large: 640px
- Mobile small: 540px (or 480px on some pages)

## Accessibility
- Always include `@media (prefers-reduced-motion: reduce)` that kills all animations
- Focus-visible outlines on interactive elements
- ARIA labels on tab groups and form controls

## File Structure
```
pmc-webflow-project/
├── CLAUDE.md              ← You are here
├── global/
│   ├── head.html          ← Site Settings > Header
│   └── footer.html        ← Site Settings > Footer (currently empty)
├── pages/
│   ├── start/             ← /start (live)
│   │   ├── head.html      ← Page head CSS
│   │   ├── body.html      ← Page body JS
│   │   └── embeds/
│   │       └── lead-form.html
│   ├── meeting/           ← /meeting (live)
│   │   ├── head.html
│   │   ├── body.html
│   │   └── embeds/
│   │       └── scheduler.html
│   ├── case-studies/      ← /case-studies (staging)
│   │   ├── head.html
│   │   ├── body.html
│   │   └── embeds/
│   │       ├── founding-client-banner.html
│   │       ├── showcase.html
│   │       ├── results.html
│   │       └── intake-form.html
│   └── home-draft/        ← /home (draft)
│       ├── head.html
│       ├── body.html
│       └── embeds/
│           ├── free-audit-banner.html
│           ├── gauges.html
│           └── contact-form.html
└── docs/
    └── website-os.md      ← Full OS documentation
```

## Output Rules (How Joe Wants Code Delivered)

1. **Always deliver COMPLETE files.** Never partial diffs, never "rest remains the same." Paste the full file every time.
2. **Use hyphens, not em dashes** in copy/text content.
3. **White text over gray on dark backgrounds** - use #F5F0E8, not gray variants.
4. **Clean transparent logos** - the PMC logo package is at `transparent/PMC_Horizontal_DarkText.png` (1886x580, native high-res).
5. When editing CSS or JS, output the ENTIRE page head or page body file, not just the changed section.

## Current Page Status
- `/start` - LIVE on pfendermarketing.com
- `/meeting` - LIVE on pfendermarketing.com
- `/terms` - LIVE (simple, no custom code beyond meta robots)
- `/privacy` - LIVE (simple, no custom code beyond meta robots)
- `/case-studies` - STAGING at pmc-staging.webflow.io
- `/home` - DRAFT (80% complete, not yet replacing /start as primary)
