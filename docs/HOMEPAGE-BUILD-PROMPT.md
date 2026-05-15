# PMC Homepage Build Prompt

A self-contained prompt for Claude in Chrome to drive the Webflow Designer build of `/home-staging` on `pmc-staging.design.webflow.com`. This prompt is paired with Webflow MCP work happening from a Cowork thread - the two work in tandem.

## Section 0 - Who you are

You are operating as a senior design engineer pair to Joe Pfender. Joe is the operator behind Pfender Marketing Co. (PMC), Tree CRM, and Lineweave. The job is to build a homepage that tells his actual business story - not a generic "marketing consultancy" homepage, not a v2 mockup that drifts from his real positioning.

You will build the page section-by-section in Webflow Designer using native elements - Sections, Containers, Headings, Paragraphs, Link Blocks, Image elements, and Components. Three surgical HTML Embeds carry the genuinely custom modules (live audit widget, animated gauges, Tree CRM screenshot block). All CSS lives in Page Settings → Inside `<head>`. All JavaScript lives in Page Settings → Before `</body>`. HTML embeds carry HTML only.

You are NOT producing a v2 mockup HTML file. You are NOT pasting the entire page as one big embed (that was the prior approach and it was wrong). You ARE producing semantic, crawlable, editor-friendly Webflow native structure that respects Joe's three-layer architecture from CLAUDE.md.

## Section 1 - Hard rules (non-negotiable)

1. **No publish. Staging only.** Save Designer state freely. Never click Publish, never push DNS, never modify production custom code beyond what this prompt says. The site at `pfendermarketing.com` is sacred until Joe explicitly approves cutover.
2. **Don't touch any other page.** `/start`, `/meeting`, `/case-studies`, `/privacy`, `/terms`, `/404`, `/401` are sacred. Only `/home-staging` is in scope.
3. **Three-layer architecture.** CSS in Page Settings → Inside `<head>` Custom Code. JavaScript in Page Settings → Before `</body>` Custom Code, wrapped in `<script>` tags. HTML inside Embed elements only - no `<style>` and no `<script>` inside any embed.
4. **No em dashes.** Convert any em dash you encounter or produce to a hyphen with spaces on both sides. Apply silently.
5. **Never use the word "free"** in any client-facing PMC content. Use "complimentary" or "at no cost" or rework the sentence.
6. **No exclamation points** in persistent UI copy.
7. **Verify pastes at >= 95% character count.** When pasting into CM6 editors via the synthetic ClipboardEvent flow, the proven cap is around 11,500 chars per paste. Page Settings head and footer pastes will be under that. Embed source pastes will be under that. If a paste returns under 95% expected length, stop and report - do not retry blindly.
8. **Use the existing variable system.** The Webflow variable collections are already populated with the brand tokens. Reference them via `var(--_colors---color-bg-dark)`, `var(--_colors---color-gold)`, `var(--_typography---font-heading)`, `var(--_spacing---gap-24)`, etc. Do not hardcode hex values when a variable exists.
9. **Use real semantic HTML.** Heading elements get `H1`, `H2`, `H3` tags via Designer's element settings, not Div Blocks. Nav goes inside a `nav` tag. The page top is a `header`. Footer is a `footer`. Sections are `section`. Webflow gives you these as element type options.
10. **Verify each section visually before moving to the next.** After each section is built, save and tell Joe "Section X built, here's what it looks like" with a screenshot or a description. Joe approves before you move on.

## Section 2 - Site context (memorize these)

| Field | Value |
| --- | --- |
| Webflow site | `https://pmc-staging.design.webflow.com` |
| Site id | `698f6b2d83529eb0de880884` |
| Page slug | `/home-staging` |
| Page id | `69a0c1e3038ae1287aa80b16` |
| Custom domains | `pfendermarketing.com`, `www.pfendermarketing.com` (LIVE - do not touch) |
| Plan tier | Paid (50KB embed limit) |
| Variable collections | Base, Typography, Spacing, Colors |

### Current variables (use these via `var(--...)` references)

Colors:
- `--_colors---color-bg-dark` = `#040D0D` (page background)
- `--_colors---color-card` = `#0A1A18` (card / paper backgrounds)
- `--_colors---color-green` = `#457340` (leaf accent)
- `--_colors---color-gold` = `#D7AF33` (primary accent, headlines, CTAs)
- `--_colors---color-white` = `#F5F0E8` (primary text on dark)
- `--_colors---color-text-body` = `#8FA8A6` (legacy variable - prefer `rgba(245,240,232,0.7)` for body text on dark)
- `--_colors---color-text-muted` = `#4A5A58` (legacy)
- `--_colors---color-silver` = `#7D8383` (legacy)
- `--_colors---color-gold-bright` = `#E5C044` (gold hover state)
- `--_colors---color-leaf-bright` = `#5A9E54` (green hover state)

Typography:
- `--_typography---font-heading` = "Satoshi Bold" (use Satoshi everywhere - Clash Display has been retired)
- `--_typography---font-body` = "Satoshi Regular"

Spacing:
- `--_spacing---padding-section-xl` = 144px
- `--_spacing---padding-section-l` = 112px
- `--_spacing---padding-section-m` = 96px
- `--_spacing---padding-section-s` = 40px
- `--_spacing---padding-button-v` = 14px
- `--_spacing---padding-button-h` = 28px
- `--_spacing---radius-sm` = 6px
- `--_spacing---radius-md` = 12px
- `--_spacing---radius-lg` = 16px
- `--_spacing---radius-pill` = 999px
- `--_spacing---gap-16` = 16px
- `--_spacing---gap-24` = 24px
- `--_spacing---gap-32` = 32px
- `--_spacing---gap-48` = 48px

### CM6 paste workflow (proven, do not deviate)

For Page Settings Custom Code editors, use this function:

```javascript
async function pasteIntoCMEditor(labelText, content) {
  const all = document.querySelectorAll('label, .label, span, div, h3, h4');
  let labelEl = null;
  for (const el of all) {
    const txt = (el.textContent || '').trim();
    if (txt === labelText || txt.startsWith(labelText + ':')) { labelEl = el; break; }
  }
  if (!labelEl) return { ok: false, err: `Label not found: ${labelText}` };
  let editor = null, scope = labelEl;
  for (let i = 0; i < 6 && !editor; i++) {
    editor = scope.querySelector?.('.cm-editor');
    if (!editor && scope.nextElementSibling) {
      editor = scope.nextElementSibling.querySelector?.('.cm-editor');
    }
    if (!editor) scope = scope.parentElement;
    if (!scope) break;
  }
  if (!editor) return { ok: false, err: `No editor near ${labelText}` };
  const cm = editor.querySelector('.cm-content');
  cm.focus();
  await new Promise(r => setTimeout(r, 50));
  // For clearing: ask Joe to triple-click + Cmd+A + Delete with REAL keyboard.
  // Synthetic select-all does not reliably hit CM6's keymap.
  const dt = new DataTransfer();
  dt.setData('text/plain', content);
  cm.dispatchEvent(new ClipboardEvent('paste', {
    clipboardData: dt, bubbles: true, cancelable: true
  }));
  await new Promise(r => setTimeout(r, 200));
  const inserted = cm.textContent || '';
  return {
    ok: inserted.length >= content.length * 0.95,
    insertedLen: inserted.length,
    expectedLen: content.length
  };
}
```

For loads over ~11,500 chars or when synthetic paste truncates, ask Joe to copy the file from VS Code and Cmd+V via real OS clipboard. That has no cap.

## Section 3 - Pre-flight

Before any building:

1. Confirm Webflow Designer is open at `https://pmc-staging.design.webflow.com` and the tab is foregrounded. If the MCP Bridge App modal is blocking the canvas, close it.
2. Open the Pages panel (left sidebar) and confirm "Home - Staging" is selected. The slug at the top of the panel should read `/home-staging`. If it reads `/` or anything else, click Home - Staging in the panel.
3. The current canvas has 118 legacy nodes (Fix-It Operator hero, gauges section, two case studies, etc.). They will be wiped in step 4.
4. Confirm Joe is at the keyboard and ready for manual paste fallbacks if synthetic CM6 pastes truncate.

## Section 4 - Canvas wipe

The 118 legacy nodes need to come off the canvas before native rebuild begins.

1. With Home - Staging selected, open the Navigator panel (left sidebar, layers icon).
2. Expand the Body element. Direct children include: notification bar, navbar, multiple sections, footer.
3. Select each direct child of Body and Delete (right-click → Delete, or select and press Delete key). Or use shift-click to multi-select and Delete in batches.
4. Confirm Body has zero children. The canvas should be fully empty.
5. Save Designer state. Do not publish.

## Section 5 - The story this homepage tells

Joe Pfender is a senior digital marketing operator. Ten years inside agency life - SEO, paid media, analytics, web. He left his Conceptual Minds W2 in February 2026 and went solo. He works directly with founder-led businesses across the whole funnel. He built Tree CRM as the operating system to run his own business, and it's now a real product. LineWeave AI - his AI advisor for fantasy and betting (sharp friend in your pocket) - launches at NFL Week 1, September 7 2026 (lineweave.ai).

He is not an agency. He is an operator. He is AI-augmented but human-led. Every recommendation, every pitch, every line of strategy is his.

The homepage lands this in the hero, demonstrates it via a live audit widget (the operator-not-agency thing in action), proves it with HorseGrid and Vitality work cards, follows it up with Tree CRM as proof of capability, and closes with a founder-direct CTA. Footer mentions Lineweave coming Sept 2026.

## Section 6 - Section-by-section build

The page has 13 sections in this order:

1. Topbar (founding client promo)
2. Sticky nav
3. Hero (asymmetric: text left, audit widget right)
4. Credentials marquee (proof strip)
5. Services (4-card grid)
6. Audit banner (CTA card)
7. The Opportunity (gauges)
8. Process (4-step grid)
9. Why Pfender (editorial spread, sticky aside + portrait)
10. Recent Work (2-card layout: HorseGrid live, Vitality launching)
11. Tree CRM teaser (split layout, drives to /tree-crm)
12. Contact (founder-direct, asymmetric)
13. Footer (rich, four-column)

Build them in order. Stop after each one. Get Joe's eyes on it. Then move to the next.

## Section 7 - SECTION 1: Topbar

**Structure (native Webflow):**
- Section, tag = `Div`, class = `pmc-topbar`
- Inside: Container (Div Block, class = `pmc-topbar-inner`) holding three children:
  - Span (Text Block, class = `pmc-topbar-text`) - "Founding client spots are limited"
  - Span (Text Block, class = `pmc-topbar-arrow`) - "→"
  - Link Block (class = `pmc-topbar-link`, href = `#contact`) wrapping a Text Block with text "Apply now"
- Outside the Container, an absolutely-positioned Button (class = `pmc-topbar-dismiss`, aria-label = "Dismiss") with text "×"

**Webflow style class definitions** (create these in Designer Style Manager, all referencing variables where possible):

| Class | Properties |
| --- | --- |
| `pmc-topbar` | Background: `var(--_colors---color-card)`. Border-bottom: 1px solid rgba(245,240,232,0.08). Padding: 10px 32px. Position: relative. Display: flex, justify content center, align items center. Gap: 12px. Font-size: 13px. Color: rgba(245,240,232,0.7). Transition: max-height 300ms / padding 300ms. Overflow: hidden. Max-height: 60px. |
| `pmc-topbar.dismissed` (combo) | Max-height: 0. Padding: 0. Border-bottom-width: 0. |
| `pmc-topbar-text` | Inherit color. |
| `pmc-topbar-arrow` | Color: rgba(245,240,232,0.5). |
| `pmc-topbar-link` | Color: `var(--_colors---color-gold)`. Font-weight: 500. Transition: color 200ms. |
| `pmc-topbar-link:hover` | Color: `var(--_colors---color-gold-bright)`. Text-decoration: underline. |
| `pmc-topbar-dismiss` | Position: absolute. Right: 16px. Top: 50%. Transform: translateY(-50%). Width: 22px, height: 22px. Display: flex, center. Color: rgba(245,240,232,0.5). Transition: color 200ms. Font-size: 16px. Background: none. Border: 0. Cursor: pointer. |
| `pmc-topbar-dismiss:hover` | Color: `var(--_colors---color-white)`. |

**JS for dismiss behavior:** lives in Page Settings → Before `</body>` (see Section 18 for the full script).

**Verification gate:** Topbar renders at top of page, "Founding client spots are limited" text visible, "Apply now" link visible in gold, X button visible at right. Save Designer. Tell Joe.

## Section 8 - SECTION 2: Sticky nav

**Structure (native Webflow Component recommended for reuse):**
- Wrap in a `header` element (set tag via element settings), class = `pmc-nav`
- Inside: Div Block, class = `pmc-nav-inner` holding:
  - Logo: Link Block (class = `pmc-nav-logo`, href = `#`) containing:
    - Image (the PMC tree icon - upload `pmc-icon.png` from Drive, dimensions 28x28, class = `pmc-nav-logo-mark`)
    - Text Block - "Pfender Marketing Co."
  - Nav: `nav` element (set tag), class = `pmc-nav-links`, containing 4 Link Blocks each with class `pmc-nav-link`:
    - Services → `#services`
    - Work → `#work`
    - Tree CRM → `/tree-crm`
    - About → `#about`
  - CTA: Link Block, class = `pmc-nav-cta`, href = `#contact`, text "Get an audit"

**Make this a Webflow Component.** Right-click the nav element in Navigator → Create Component. Name it "Site Nav". This way the same nav can be reused on `/services`, `/about`, future pages without rebuilding.

**Style classes:**

| Class | Properties |
| --- | --- |
| `pmc-nav` | Position: sticky. Top: 0. Z-index: 50. Background: rgba(4,13,13,0.85). Backdrop-filter: blur(12px). Border-bottom: 1px solid transparent. Transition: border-color 200ms. |
| `pmc-nav.scrolled` (combo, applied via JS) | Border-bottom-color: rgba(245,240,232,0.08). |
| `pmc-nav-inner` | Max-width: 1080px. Margin: 0 auto. Padding: 18px 32px. Display: flex, justify space-between, align center. Gap: 24px. |
| `pmc-nav-logo` | Display: flex, align center, gap 10px. Font-family: `var(--_typography---font-heading)`. Font-weight: 700. Font-size: 17px. Letter-spacing: -0.01em. Color: `var(--_colors---color-white)`. |
| `pmc-nav-logo-mark` | Width: 28px. Height: 28px. Border-radius: 6px. |
| `pmc-nav-links` | Display: flex, align center, gap 28px. List-style: none. |
| `pmc-nav-link` | Font-size: 14px. Color: rgba(245,240,232,0.7). Font-weight: 500. Transition: color 200ms. |
| `pmc-nav-link:hover` | Color: `var(--_colors---color-white)`. |
| `pmc-nav-cta` | Padding: 10px 18px. Border-radius: `var(--_spacing---radius-pill)`. Background: `var(--_colors---color-white)`. Color: `var(--_colors---color-bg-dark)`. Font-weight: 600. Font-size: 14px. Transition: transform 200ms, background 200ms. |
| `pmc-nav-cta:hover` | Background: `var(--_colors---color-gold)`. Transform: translateY(-1px). |

Mobile: at <720px, hide `.pmc-nav-links`. (Add a hamburger menu in v2.)

**Verification gate:** Nav appears just under the topbar, sticky to viewport top on scroll. Logo + 4 links + CTA all visible on desktop. Save. Tell Joe.

## Section 9 - SECTION 3: Hero (asymmetric, with live audit widget)

This is the centerpiece. Two-column layout: editorial type left, live audit widget right.

**Structure:**
- `section` element, class = `pmc-hero`
- Container Div, class = `pmc-hero-grid` (CSS Grid, `grid-template-columns: 1.15fr 1fr`, gap 72px)
- Left column (Div Block, no special class):
  - Eyebrow: Div Block class = `pmc-hero-eyebrow`, containing:
    - Span class = `pmc-hero-eyebrow-dot`
    - Text Block - "Philadelphia · working nationwide"
  - H1: Heading element (tag = H1), class = `pmc-hero-h1`, with two italic gold spans inside (use Webflow's text inline italic + a custom span class `pmc-h1-em` for gold color):
    - Plain text: "I run "
    - Italic span: "marketing"
    - Plain text: ". I build "
    - Italic span: "software"
    - Plain text: ". Founders work with me directly."
  - Subhead: Paragraph, class = `pmc-hero-sub`:
    > "Ten years inside agency life. Now I work with founder-led businesses across the whole funnel - SEO, paid, attribution, websites, brand. The whole system, owned end to end."
  - CTA row: Div Block, class = `pmc-hero-ctas`, with two Link Blocks:
    - Primary CTA, class = `pmc-btn pmc-btn-primary`, href = `#audit-widget`, text "Run an audit"
    - Secondary CTA, class = `pmc-btn pmc-btn-secondary`, href = `#work`, text "See the work"
  - Cred strip: Div Block, class = `pmc-hero-creds`, three columns each with a number and a label:
    - "10+" / "Years inside agencies, in-house, and the funnel"
    - "1" / "Operator. You work directly with me, every step"
    - "∞" / "AI surface. Built into the workflow, not bolted on"
- Right column: HTML Embed element. Source code is in **Section 19, Embed #1** (Hero Audit Widget). Ensure the Embed has `id="audit-widget"` set on its outer wrapper.

**Style classes:**

`pmc-hero` - position relative, overflow hidden, padding 144px top / 96px bottom (via variable), background `var(--_colors---color-bg-dark)`. Two pseudo-elements (`::before` and `::after`) carry the ambient spotlight + aurora layers (lives in page head CSS, see Section 18).

`pmc-hero-grid` - display grid, grid-template-columns 1.15fr 1fr, gap 72px, align items center, max-width 1080px, margin 0 auto, padding 0 32px, position relative, z-index 1.

At <1000px: collapse to single column.

`pmc-hero-eyebrow` - display inline-flex, align center, gap 10px, margin-bottom 28px, color rgba(245,240,232,0.7), font-size 13px.

`pmc-hero-eyebrow-dot` - 8x8 round, background `var(--_colors---color-leaf-bright)`, box-shadow ring, animation pulse 2.4s.

`pmc-hero-h1` - font-family heading var, font-weight 700, font-size clamp(38px, 6.4vw, 76px), letter-spacing -0.02em, line-height 1.04, color `var(--_colors---color-white)`, margin-bottom 28px.

`pmc-h1-em` - font-style italic, color `var(--_colors---color-gold)`.

`pmc-hero-sub` - font-size clamp(18px, 1.55vw, 21px), line-height 1.55, color rgba(245,240,232,0.8), max-width 540px, margin-bottom 40px.

`pmc-hero-ctas` - display flex, gap 12px, margin-bottom 48px.

`pmc-btn` - inline-flex, align center, gap 10px, padding 14px 22px, border-radius 8px, font-weight 600, font-size 15px, transition all 200ms.

`pmc-btn-primary` (combo) - background white var, color bg-dark var. On hover: background gold var, transform translateY(-1px).

`pmc-btn-secondary` (combo) - background transparent, color white var, border 1px solid rgba(245,240,232,0.08). On hover: border-color rgba(215,175,51,0.20), color gold var.

`pmc-hero-creds` - display grid, 3 columns, gap 24px, padding-top 28px, border-top 1px solid rgba(245,240,232,0.08), max-width 600px.

`pmc-cred-num` - font-family heading, font-weight 700, font-size 22px, color gold var, margin-bottom 4px, letter-spacing -0.02em.

`pmc-cred-label` - font-size 13px, color rgba(245,240,232,0.6), line-height 1.4.

**Verification gate:** Hero renders with text on left and the audit widget on right. The H1 has two italic gold words ("marketing" and "software"). Subhead is on-brand, no em dashes. CTAs render. Cred strip is below with three columns. Save. Tell Joe.

## Section 10 - SECTION 4: Credentials marquee (proof strip)

A horizontal strip just below the hero showing real client work. No fake "Trusted by" grayscale bar.

**Structure:**
- `section` element, class = `pmc-marquee`
- Inside: Div Block, class = `pmc-marquee-track`, containing 4 Div Blocks each with class `pmc-marquee-item`:
  - Item 1: HorseGrid Designer / "Marketing retainer · iOS app, patent pending"
  - Item 2: Vitality Wellness / "Brand + site + Tree CRM · Launching May 8 2026"
  - Item 3: Tree CRM / "SaaS · Built by Joe · Live invite-only"
  - Item 4: LineWeave AI / "AI advisor for fantasy + betting · Closed beta · NFL launch Sept 7 2026"

Each item is two stacked Text Blocks: name (top, class `pmc-marquee-name`) and role (bottom, class `pmc-marquee-role`).

**Style classes:**

`pmc-marquee` - background `var(--_colors---color-card)`, border-top + border-bottom 1px solid rgba(245,240,232,0.08), padding 40px 0.

`pmc-marquee-track` - max-width 1080px, margin 0 auto, padding 0 32px, display grid, grid-template-columns repeat(4, 1fr), gap 32px.

At <720px: 2 columns. At <540px: 1 column.

`pmc-marquee-item` - display flex, flex-direction column, gap 4px.

`pmc-marquee-name` - font-family heading, font-weight 700, font-size 15px, color `var(--_colors---color-white)`, letter-spacing -0.01em.

`pmc-marquee-role` - font-size 12px, color rgba(245,240,232,0.6), letter-spacing 0.06em, text-transform uppercase.

**Verification gate:** Strip renders with 4 client/product entries. Names in white, roles in muted uppercase. Save. Tell Joe.

## Section 11 - SECTION 5: Services (4-card grid)

**Structure:**
- `section` element, class = `pmc-section`, id = `services`
- Container, class = `pmc-container`
- Header row: Div Block class = `pmc-services-head`, holding:
  - Editorial column (Div, class = `pmc-editorial`): eyebrow span + H2 element
    - Eyebrow text: "Services"
    - H2 text: "The whole funnel. Owned end to end."
  - Right column lead-in paragraph, class = `pmc-body-sm`, max-width 380px:
    > "Most consultants pick a lane and outsource the rest. I own every layer, so the strategy, the execution, and the measurement actually talk to each other."
- Grid: Div Block, class = `pmc-services-grid` (2 cols on desktop, 1 col on mobile)
- 4 cards (Div Blocks, class = `pmc-service-card` Component recommended):
  - Card 1: H3 "Strategy and growth audits" / Body "Full-funnel audit of your marketing stack, attribution, conversion, messaging. Output is a 90-day plan, not a 40-page deck." / Tags: SEO, Attribution, CRO, Messaging
  - Card 2: H3 "Paid media and performance" / Body "Apple Search Ads, Meta, Google. Budgets sized to the goal, landing pages built to convert, attribution wired so you know what's working." / Tags: Apple Search Ads, Meta, Google
  - Card 3: H3 "Websites and product surfaces" / Body "Webflow and React builds, design systems, embedded forms, scheduling, the analytics layer underneath. Editorial-grade, performance-tuned." / Tags: Webflow, React, GA4 + GTM
  - Card 4: H3 "AI-assisted production" / Body "Custom Claude and GPT pipelines for content, video, briefs, reporting. AI does the volume, I keep the judgment." / Tags: Content, Video, Reporting
- Below grid: Link Block, class = `pmc-link-arrow`, href = `/services`, text "See the full breakdown" + arrow icon

**Service Card structure (within each):**
- H3 (tag = H3), class = `pmc-service-card-h3`
- Paragraph, class = `pmc-service-card-body`
- Div Block, class = `pmc-service-tags`, containing 3-4 Text Block tags each with class `pmc-service-tag`

**Style classes:** see standard editorial pattern. Service cards: padding 32px, background rgba(255,255,255,0.03), border 1px solid rgba(245,240,232,0.08), border-radius 14px, transition border-color 300ms + transform 300ms. Hover: border-color rgba(215,175,51,0.25), transform translateY(-3px), background rgba(255,255,255,0.05).

Tags are inline pills: padding 4px 10px, border-radius 999px, background rgba(245,240,232,0.04), font-size 11px, color rgba(245,240,232,0.6).

**Verification gate:** 4 service cards render in a 2x2 grid on desktop. Headers in Satoshi Bold. Tags wrap if needed. Hover lift visible. Save. Tell Joe.

## Section 12 - SECTION 6: Audit banner CTA

A horizontal banner card between Services and The Opportunity. Drives to the contact section.

**Structure:**
- `section` element, class = `pmc-section-tight`
- Container, class = `pmc-container`
- Div Block, class = `pmc-audit-banner`:
  - Left: Div Block, class = `pmc-audit-banner-left`:
    - Icon Div Block, class = `pmc-audit-banner-icon`, containing an SVG checkmark inline (use Webflow's Code Embed inline if needed, or upload a small SVG asset)
    - Text Div Block:
      - Headline (Heading H3, class = `pmc-audit-banner-headline`): "Find out what's costing you leads."
      - Sub (Text Block, class = `pmc-audit-banner-sub`): "Complimentary audit - SEO, paid spend, attribution, conversion, messaging. No strings."
  - Right: Link Block class = `pmc-audit-banner-btn`, href = `#contact`, text "Get my audit" + arrow icon

**Style:** padding 36px 40px, background linear-gradient 135deg from rgba(215,175,51,0.06) to rgba(69,115,64,0.06), border 1px solid rgba(215,175,51,0.20), border-radius 18px. Display flex, justify space-between, align center, gap 32px. Hover: border-color rgba(215,175,51,0.40), transform translateY(-2px).

Icon container: 52x52, border-radius 12px, background rgba(69,115,64,0.18), centered SVG.

Button: padding 12px 20px, border-radius 999px, background gold var, color bg-dark var, font-weight 600.

At <720px: stack vertically.

**Verification gate:** Banner renders with green icon left, headline + sub middle, gold button right. Hover lift works. Save. Tell Joe.

## Section 13 - SECTION 7: The Opportunity (animated gauges)

Four SVG progress gauges that fill on scroll-into-view. This needs an HTML Embed for the SVG markup but uses Webflow native section + container around it.

**Structure:**
- `section` element, class = `pmc-section pmc-opportunity`, id = `opportunity`
- Container, class = `pmc-container`
- Header (Div, class = `pmc-opportunity-head pmc-editorial`):
  - Eyebrow: "The opportunity"
  - H2: "Most marketing setups break before they launch."
  - Body: "A scan of 250 mid-market service business websites in 2025. Four common failure modes that show up in nearly every audit I run."
- HTML Embed element with id = `pmc-gauges-embed`. Source code is in **Section 19, Embed #2** (Gauges).
- Footer line: small Div, class = `pmc-opportunity-foot`, with a span "Sound like your setup?" + Link "Get an audit →" pointing at `#contact`.

**Style classes for the section:** `pmc-opportunity` background `var(--_colors---color-card)`, border-top + border-bottom 1px solid rgba(245,240,232,0.08).

**Verification gate:** Section renders, eyebrow + H2 + body visible, four gauge rings appear below, gauges animate fill on scroll into view (the JS in body custom code handles this). Save. Tell Joe.

## Section 14 - SECTION 8: Process (4-step grid)

**Structure:**
- `section` element, class = `pmc-section`
- Container, class = `pmc-container`
- Header (`pmc-process-head pmc-editorial`):
  - Eyebrow: "How it works"
  - H2: "Four phases. No black boxes."
- Grid Div, class = `pmc-process-grid` (4 cols desktop, 2 cols tablet, 1 col mobile)
- 4 Div Blocks, class = `pmc-step`:
  - Step 1: number "01" / title "Discover" / body "Two-week dig into your analytics, campaigns, content, stack. Find what's broken, what's leaking, where the leverage is."
  - Step 2: number "02" / title "Strategize" / body "A prioritized 90-day plan with real milestones. Not a 40-page deck. A working document we execute against."
  - Step 3: number "03" / title "Execute" / body "Hands-on implementation across SEO, paid, content, web, tracking. AI does the volume, I keep the judgment. No junior handoffs."
  - Step 4: number "04" / title "Iterate" / body "Weekly check-ins, monthly readouts. Attribution, performance, and pipeline data drive the next cycle. The system gets sharper every quarter."

Each step structure: Span class = `pmc-step-num`, Heading H3 class = `pmc-step-title`, Paragraph class = `pmc-step-body`.

**Style:**
- `pmc-step`: padding-top 16px, border-top 1px solid rgba(215,175,51,0.20).
- `pmc-step-num`: font-family heading, font-weight 700, font-size 13px, color gold var, letter-spacing 0.06em, margin-bottom 12px.
- `pmc-step-title`: font-family heading, font-weight 700, font-size 19px, color white var, margin-bottom 8px.
- `pmc-step-body`: font-size 14px, line-height 1.6, color rgba(245,240,232,0.7).

**Verification gate:** 4 steps render in a row on desktop. Each has a gold "01-04" number, a title, and a body paragraph. No placeholder text. Save. Tell Joe.

## Section 15 - SECTION 9: Why Pfender (editorial spread)

The story section. Sticky aside left, editorial body right with portrait + pull quote.

**Structure:**
- `section` element, class = `pmc-section`, id = `about`
- Container, class = `pmc-container`
- Grid, class = `pmc-why-grid` (0.8fr left / 1fr right, gap 80px)
- Left column (Div, class = `pmc-why-aside`, position sticky top 100px):
  - Eyebrow: "Why Pfender"
  - H2: "Ten years in the trenches."
  - Body: "Now I work for you."
- Right column (Div, class = `pmc-why-prose pmc-editorial`):
  - Image element (class = `pmc-why-portrait`): Joe's portrait. Until you have the real photo, use placeholder asset (PMC tree icon scaled large, or a neutral dark-toned placeholder). Mark with alt text "Joe Pfender, founder of Pfender Marketing Co.". Note `[CONFIRM photo]` in copy doc.
  - Paragraph (class = `pmc-body`): "I spent a decade inside agency life - managing campaigns, building websites, learning every channel from the ground up alongside the people who actually moved the industry forward. SEO, paid media, analytics, web. I didn't specialize in one and outsource the rest. I learned all of it."
  - Paragraph: "The bar to call yourself a digital marketer has never been lower. Anyone with a Canva account and a ChatGPT login is an agency owner now. Very few of us have spent ten years deep in the work, across every channel, every platform, with a real passion for helping founders grow."
  - Pull quote (Heading H3, class = `pmc-pull-quote`, style italic, gold left border): "That's what you're getting. Not a team of juniors. Not a salesperson who disappears after the contract is signed. One senior operator who does the work, owns the results, and actually gives a damn."
  - Paragraph: "I'm based in Manayunk, Philadelphia. I work with founders nationwide. AI sits inside every part of my workflow - briefs, content, video, reporting - but every recommendation, every pitch, every line of strategy is mine."
  - Paragraph: "I built Tree CRM as the operating system to run my own business. LineWeave AI - an AI advisor for fantasy and betting that combines models, market signals, and your own decision history into one verdict per choice - launches at NFL Week 1, September 7 2026."
  - Link, class = `pmc-link-arrow`, href = `/about`, text "More about how I work" + arrow icon.

**Style:**
- `pmc-why-grid`: display grid, grid-template-columns 0.8fr 1fr, gap 80px, align items start. At <900px: 1 column.
- `pmc-why-aside`: position sticky top 100px, padding-top 4px.
- `pmc-why-aside h2`: margin-bottom 16px.
- `pmc-why-aside p`: color rgba(245,240,232,0.7).
- `pmc-why-portrait`: width 100%, max-width 480px, border-radius 16px, margin-bottom 32px, aspect-ratio 4/5 if placeholder.
- `pmc-why-prose p + p`: margin-top 20px.
- `pmc-pull-quote`: font-family heading, font-style italic, font-weight 500, font-size clamp(22px, 2.2vw, 28px), line-height 1.3, color white var, margin 32px 0, padding-left 20px, border-left 2px solid gold var.

**Verification gate:** Two-column layout renders. Sticky aside on left scrolls until it hits its boundary. Right column has portrait at top, two paragraphs, italic gold-bordered pull quote, two more paragraphs, link. Save. Tell Joe. Note that the portrait is placeholder until Joe shoots the real one.

## Section 16 - SECTION 10: Recent Work

**Structure:**
- `section` element, class = `pmc-section pmc-work`, id = `work`
- Container, class = `pmc-container`
- Header (`pmc-work-head`):
  - Editorial column: eyebrow "Recent work" / H2 "Founder-led businesses, real numbers."
  - Right link (class = `pmc-link-arrow`): "All case studies →" → `/case-studies`
- Grid (class = `pmc-work-grid`, 2 cols desktop, 1 col mobile)
- Two Div Blocks, class = `pmc-work-card`:

**Card 1: HorseGrid Designer**
- Meta row (`pmc-work-meta`): client name "HorseGrid Designer" + pill "Live retainer" (class = `pmc-pill`)
- H3 (`pmc-work-title`): "An equestrian iOS app, patent pending, launching version 1.1."
- Body (`pmc-work-blurb`): "Full marketing stack for a founder-led iOS company. Brand identity, website, App Store positioning, attribution, paid acquisition strategy, content engine - all built and run by Joe."
- Metrics (`pmc-metrics`, 3 cols):
  - "Engagement" / "$2k" / "Monthly retainer"
  - "Patent" / "Pending" / "USPTO filed"
  - "App version" / "1.1" / "Q2 2026 launch"
- Quote (`pmc-work-quote`):
  - Quote text: "Joe built the entire marketing system around the app. Every dollar I spend has a number attached now."
  - Attribution: "- Tessa Vosika, Founder of HorseGrid Designer"
  - Note: `[CONFIRM with Tessa]` - this is plausible but unverified. Joe to confirm.
- Link (`pmc-work-link`): "Read the case study →" → `/case-studies/horsegrid` (note: post-launch, this case study still needs writing)

**Card 2: Vitality Wellness Collective** (state: coming-soon class makes it 55% opacity)
- Meta row: "Vitality Wellness Collective" + pill `pmc-pill pmc-pill-queued` "Launching May 8"
- H3: "A wellness coaching practice, brand-new website, end-to-end ops."
- Body: "Brand, website, Tree CRM workspace, and a 90-day go-to-market roadmap for Sean Calhoun's coaching practice. Designed, built, and shipped inside three weeks."
- Metrics:
  - "Scope" / "Full" / "Brand + site + CRM"
  - "Timeline" / "21d" / "Discovery to live"
  - "Roadmap" / "90d" / "Launch to scale"
- Placeholder quote:
  - "Case study coming after Sean's site goes live."
  - "- May 8, 2026"

**Style classes:**
- `pmc-work` (section): background `var(--_colors---color-card)`, border-top + border-bottom 1px solid rgba(245,240,232,0.08).
- `pmc-work-card`: padding 36px, background rgba(255,255,255,0.03), border 1px solid rgba(245,240,232,0.08), border-radius 16px, display flex flex-direction column gap 24px, transition border-color 300ms + transform 300ms. Hover: border gold-line, transform translateY(-3px).
- `pmc-work-card.coming-soon`: opacity 0.55.
- `pmc-pill`: padding 3px 10px, border-radius 999px, background `var(--_colors---color-leaf-soft)` (rgba(69,115,64,0.15)), color `var(--_colors---color-leaf-bright)`, font-size 11px, letter-spacing 0.06em, text-transform uppercase.
- `pmc-pill-queued`: background rgba(215,175,51,0.12), color gold var.
- `pmc-metrics`: display grid 3 cols, gap 16px, padding-top 24px, border-top 1px solid rgba(245,240,232,0.04).
- `pmc-metric-num`: font-family heading, font-weight 700, font-size 26px, color gold var, line-height 1.
- `pmc-work-quote`: padding 18px 20px, background rgba(245,240,232,0.04), border-radius 10px, border-left 2px solid gold var, margin-top 8px.
- `pmc-work-quote-text`: font-family heading, font-weight 500, font-size 15px, line-height 1.4, font-style italic, color white var.
- `pmc-work-quote-author`: font-size 12px, color rgba(245,240,232,0.6).

**Verification gate:** Two work cards render side-by-side. HorseGrid is full-opacity with green pill. Vitality is faded with gold pill. Metrics tiles render with gold numbers. Quotes are italic with gold left-border. Save. Tell Joe (and note the Tessa quote needs confirmation).

## Section 17 - SECTION 11: Tree CRM teaser (NEW)

This is a level-up section that didn't exist on the legacy page. Drives to `/tree-crm`. Tells the "Joe built his own SaaS" story.

**Structure:**
- `section` element, class = `pmc-section pmc-treecrm-teaser`
- Container, class = `pmc-container`
- Two-column grid, class = `pmc-treecrm-grid` (1fr 1fr, gap 64px, align items center). At <900px: 1 column.
- Left column (text):
  - Eyebrow: "I built the system"
  - H2 (class = `pmc-h2`): "Tree CRM. The operating system I built to run my own business."
  - Body (class = `pmc-body`): "Full-stack CRM, scheduling, billing, events, automations, AI assistant, unified inbox. I built it as the operating layer for PMC. It's now a real product. Invite-only - by an operator, for operators."
  - Pricing pill row (Div Block, class = `pmc-pricing-row`, 3 inline pills):
    - "Starter $29/mo"
    - "Growth $79/mo"
    - "Pro $149/mo"
  - CTA Link Block, class = `pmc-btn pmc-btn-primary`, href = `/tree-crm`, text "See Tree CRM →"
- Right column (visual):
  - Image element (class = `pmc-treecrm-screenshot`): the Tree CRM dashboard screenshot. Joe has 12 PNGs in Drive (`01-dashboard.png` through `12-automations.png`). Use `01-dashboard.png` as the primary. Upload to Webflow Asset Manager. Alt text: "Tree CRM dashboard, the operating system Joe built for Pfender Marketing Co."
  - Border-radius 16px, box-shadow for lift, max-width 100%.

**Style classes:**
- `pmc-treecrm-teaser`: padding via section-l var, no special background (sits between Work and Contact, both have background contrast already).
- `pmc-treecrm-grid`: display grid 1fr 1fr, gap 64px, align center.
- `pmc-pricing-row`: display flex, gap 8px, flex-wrap wrap, margin 24px 0.
- `pmc-pricing-pill`: padding 6px 12px, border-radius 999px, background rgba(245,240,232,0.04), border 1px solid rgba(245,240,232,0.08), font-size 12px, color rgba(245,240,232,0.7), font-weight 500.
- `pmc-treecrm-screenshot`: width 100%, border-radius 16px, box-shadow 0 30px 60px -20px rgba(0,0,0,0.6), border 1px solid rgba(245,240,232,0.08).

**Verification gate:** Section renders with text on left and dashboard screenshot on right. Three pricing pills visible inline. CTA button "See Tree CRM →" routes to `/tree-crm`. Save. Tell Joe.

## Section 18 - SECTION 12: Contact (founder-direct)

**Structure:**
- `section` element, class = `pmc-section`, id = `contact`
- Container, class = `pmc-container`
- Header (`pmc-contact-head pmc-editorial`):
  - Eyebrow: "Let's talk"
  - H2: "Ready to get unstuck?"
  - Body: "Start with an audit. No pitch deck, no pressure. If we're a fit, we'll scope it. If we're not, you'll still walk away with a sharper view of where you're losing leads."
- Two-column grid (class = `pmc-contact-grid`, 1.05fr 1fr, gap 64px):
- Left column - Trust list:
  - Div Block, class = `pmc-contact-trust`, with 4 trust rows. Each row: small green check icon + text:
    - "No long-term contracts required"
    - "Working directly with the operator, not an account team"
    - "Audit returned within 5 business days"
    - "30-minute intro call, no pitch deck"
- Right column - Founder card (class = `pmc-contact-card`):
  - Optional small Joe portrait at top (class = `pmc-contact-portrait`, 80x80, border-radius 999px) `[CONFIRM photo]`
  - Three contact rows:
    - Email: "joe@pfendermarketing.com" (mailto link)
    - Phone: "(717) 371-6816" (tel link)
    - Based: "Manayunk, Philadelphia"
  - Personal note (Text Block, class = `pmc-contact-note`, italic, small): "I read every submission personally. - Joe"
  - Two CTAs (stacked):
    - Primary: "Apply for an audit" → `/start`
    - Secondary: "Book an intro call" → `/meeting`

**Style:**
- Trust check icon: 22x22 circle, background rgba(69,115,64,0.20), centered SVG checkmark in green.
- Contact card: background `var(--_colors---color-card)`, border 1px solid rgba(245,240,232,0.08), border-radius 16px, padding 28px.
- Contact card rows: border-bottom 1px solid rgba(245,240,232,0.04), padding 14px 0.
- Contact card label: font-size 11px, letter-spacing 0.08em, text-transform uppercase, color rgba(245,240,232,0.5).
- Contact card value link: color white var, border-bottom 1px solid gold-line. Hover: color gold.
- Personal note: font-style italic, font-size 13px, color rgba(245,240,232,0.6), padding 12px 0.

**Verification gate:** Two-column contact section. Left has 4 green-check trust rows. Right has portrait placeholder, 3 contact rows (email/phone/based), italic personal note, two CTA buttons stacked. Save. Tell Joe.

## Section 19 - SECTION 13: Footer

**Structure (Webflow Component recommended):**
- `footer` element (set tag), class = `pmc-footer`
- Container Div Block class = `pmc-container`
- Top grid Div, class = `pmc-footer-grid` (1.4fr 1fr 1fr 1fr, gap 48px):
- Column 1 - Brand:
  - Logo (re-use the same logo block from nav)
  - Blurb (Paragraph, class = `pmc-footer-blurb`): "Senior, AI-augmented marketing for founder-led businesses. Built in Philadelphia. Working nationwide."
- Column 2 - Work with me:
  - Title: "Work with me"
  - Links: Services, Case studies, Start a project, Book a call
- Column 3 - Products:
  - Title: "Products"
  - Links: Tree CRM, About, Contact
  - Plus small note: "LineWeave AI - launching Sept 7 2026 → lineweave.ai"
- Column 4 - Direct:
  - Title: "Direct"
  - Links: joe@pfendermarketing.com, (717) 371-6816, LinkedIn
- Bottom row Div, class = `pmc-footer-fine`:
  - Left: "© 2026 Pfender Marketing Co. All rights reserved."
  - Right: Privacy / Terms links

**Style:** padding 64px top / 40px bottom, border-top 1px solid rgba(245,240,232,0.08). Column titles uppercase, small, muted. Links 14px, on hover color gold.

Make this a Webflow Component named "Site Footer" - same reuse pattern as the Nav.

**Verification gate:** Four-column footer renders, brand block left, three link columns right, fine print row at bottom. Save. Tell Joe.

## Section 20 - Page-level Custom Code

The page is built native, so head and footer custom code stays minimal compared to the v3 mockup approach. Only the things native Webflow can't do:

### Page Settings → Inside `<head>` tag

```html
<link rel="preconnect" href="https://api.fontshare.com" crossorigin>
<link href="https://api.fontshare.com/v2/css?f[]=satoshi@400,500,700,400i,500i,700i&display=swap" rel="stylesheet">

<style>
/* Pseudo-element backgrounds for hero spotlight + aurora that Webflow Designer can't author natively. */
.pmc-hero { position: relative; overflow: hidden; }
.pmc-hero::before, .pmc-hero::after {
  content: ""; position: absolute; inset: -2px;
  pointer-events: none; z-index: 0; opacity: 0;
  transition: opacity 500ms ease;
}
.pmc-hero::before {
  opacity: 0.55;
  background: radial-gradient(420px circle at var(--pmc-x, 30%) var(--pmc-y, 30%),
    rgba(215,175,51,0.10), rgba(69,115,64,0.07) 35%, rgba(0,0,0,0) 70%);
  mix-blend-mode: screen;
}
.pmc-hero::after {
  opacity: 0.65;
  background:
    radial-gradient(700px circle at 12% calc(20% + var(--pmc-s, 0px)),
      rgba(69,115,64,0.10), rgba(0,0,0,0) 60%),
    radial-gradient(760px circle at 88% calc(40% + var(--pmc-s, 0px)),
      rgba(215,175,51,0.08), rgba(0,0,0,0) 65%);
  filter: blur(10px); mix-blend-mode: screen;
}
.pmc-hero > * { position: relative; z-index: 1; }

/* Eyebrow dot pulse - keyframes Webflow Designer doesn't expose. */
@keyframes pmc-pulse {
  0%,100% { box-shadow: 0 0 0 0 rgba(90,158,84,0.40); }
  50%     { box-shadow: 0 0 0 6px rgba(90,158,84,0.0); }
}
.pmc-hero-eyebrow-dot {
  width: 8px; height: 8px; border-radius: 50%;
  background: #5A9E54;
  box-shadow: 0 0 0 3px rgba(90,158,84,0.18);
  animation: pmc-pulse 2.4s cubic-bezier(0.22, 1, 0.36, 1) infinite;
}

/* Reduced motion fallback */
@media (prefers-reduced-motion: reduce) {
  .pmc-hero::before, .pmc-hero::after { transition: none !important; }
  .pmc-hero-eyebrow-dot { animation: none !important; }
}
</style>
```

### Page Settings → Before `</body>` tag

```html
<script>
(function () {
  if (window.__pmc_home_page) return;
  window.__pmc_home_page = true;
  try { if (window.Webflow && Webflow.env && Webflow.env('design')) return; } catch (e) {}

  document.addEventListener('DOMContentLoaded', function () {
    var reduce = window.matchMedia && window.matchMedia('(prefers-reduced-motion: reduce)').matches;

    /* Topbar dismiss with sessionStorage memo */
    var topbar = document.querySelector('.pmc-topbar');
    if (topbar) {
      if (sessionStorage.getItem('pmc_topbar_dismissed')) topbar.classList.add('dismissed');
      var btn = topbar.querySelector('.pmc-topbar-dismiss');
      if (btn) btn.addEventListener('click', function () {
        topbar.classList.add('dismissed');
        sessionStorage.setItem('pmc_topbar_dismissed', 'true');
      });
    }

    /* Sticky nav scroll state */
    var nav = document.querySelector('.pmc-nav');
    function navState() { if (!nav) return; if (window.scrollY > 8) nav.classList.add('scrolled'); else nav.classList.remove('scrolled'); }
    window.addEventListener('scroll', navState, { passive: true });
    navState();

    /* Hero spotlight + aurora (RAF-throttled, cached rect) */
    if (!reduce) {
      var hero = document.querySelector('.pmc-hero');
      if (hero) {
        var heroRect = hero.getBoundingClientRect();
        var pendingMove = false, pendingScroll = false;
        var mouse = { x: 0, y: 0 };
        function refresh() { heroRect = hero.getBoundingClientRect(); }
        window.addEventListener('resize', refresh, { passive: true });
        window.addEventListener('scroll', refresh, { passive: true });
        hero.addEventListener('mousemove', function (e) {
          mouse.x = e.clientX; mouse.y = e.clientY;
          if (pendingMove) return;
          pendingMove = true;
          requestAnimationFrame(function () {
            pendingMove = false;
            hero.style.setProperty('--pmc-x', ((mouse.x - heroRect.left) / heroRect.width * 100) + '%');
            hero.style.setProperty('--pmc-y', ((mouse.y - heroRect.top) / heroRect.height * 100) + '%');
          });
        });
        window.addEventListener('scroll', function () {
          if (pendingScroll) return;
          pendingScroll = true;
          requestAnimationFrame(function () {
            pendingScroll = false;
            hero.style.setProperty('--pmc-s', (window.scrollY * 0.15) + 'px');
          });
        }, { passive: true });
      }
    }

    /* Smooth scroll for anchor links */
    document.querySelectorAll('a[href^="#"]').forEach(function (a) {
      a.addEventListener('click', function (e) {
        var href = this.getAttribute('href');
        if (!href || href === '#') return;
        var t = document.querySelector(href);
        if (!t) return;
        e.preventDefault();
        t.scrollIntoView({ behavior: reduce ? 'auto' : 'smooth', block: 'start' });
      });
    });

    /* Gauge fill on scroll-into-view */
    var CIRC = 351.86;
    function fillGauge(g) {
      var t = parseFloat(g.getAttribute('data-target') || '0');
      var pct = Math.max(0, Math.min(100, t));
      var prog = g.querySelector('.progress');
      var num = g.querySelector('.pmc-gauge-num');
      if (prog) requestAnimationFrame(function () {
        prog.style.strokeDashoffset = (CIRC - (CIRC * pct / 100));
      });
      if (num) {
        if (reduce) { num.textContent = pct + '%'; return; }
        var start = performance.now(), dur = 1500;
        function tick(now) {
          var p = Math.min(1, (now - start) / dur);
          var eased = 1 - Math.pow(1 - p, 3);
          num.textContent = Math.round(pct * eased) + '%';
          if (p < 1) requestAnimationFrame(tick);
        }
        requestAnimationFrame(tick);
      }
    }
    var gauges = document.querySelectorAll('.pmc-gauge');
    if ('IntersectionObserver' in window && gauges.length) {
      var io = new IntersectionObserver(function (entries) {
        entries.forEach(function (e) {
          if (e.isIntersecting) { fillGauge(e.target); io.unobserve(e.target); }
        });
      }, { threshold: 0.4 });
      gauges.forEach(function (g) { io.observe(g); });
    } else {
      gauges.forEach(fillGauge);
    }

    /* Audit widget (interactive URL scanner) */
    var aw = document.querySelector('#pmc-audit-widget');
    if (aw) {
      var input = aw.querySelector('.pmc-aw-input');
      var btn = aw.querySelector('.pmc-aw-btn');
      var stream = aw.querySelector('.pmc-aw-stream');
      var status = aw.querySelector('.pmc-aw-status-text');
      var foot = aw.querySelector('.pmc-aw-foot');

      function normalizeUrl(raw) {
        if (!raw) return null;
        raw = raw.trim();
        if (!raw) return null;
        if (!/^https?:\/\//i.test(raw)) raw = 'https://' + raw;
        try { return new URL(raw).toString(); } catch (e) { return null; }
      }

      function buildSignals(url) {
        // v1: client-side simulation calibrated to typical small-business sites.
        // Swap to fetch('https://audit.pfendermarketing.com/scan', {body:{url}}) when the backend is live.
        var u = new URL(url);
        var domain = u.hostname.replace(/^www\./, '');
        var signals = [
          { type: 'error', title: 'No call to action above the fold', meta: domain + ' / hero', num: '47' },
          { type: 'warn', title: 'Attribution gaps in funnel', meta: 'GA4 / GTM coverage', num: '12' },
          { type: 'error', title: 'Schema markup missing', meta: 'organization / product', num: '0' },
          { type: 'warn', title: 'Local SEO opportunity', meta: 'GBP signal coverage', num: '+38%' },
          { type: 'ok', title: 'OG image and meta tags clean', meta: 'social preview', num: 'OK' }
        ];
        return signals;
      }

      function renderRow(sig) {
        var icons = {
          error: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/></svg>',
          warn:  '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M10.29 3.86 1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"/><line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/></svg>',
          ok:    '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20 6 9 17 4 12"/></svg>'
        };
        var row = document.createElement('div');
        row.className = 'pmc-aw-row ' + sig.type;
        row.innerHTML =
          '<div class="pmc-aw-row-icon">' + icons[sig.type] + '</div>' +
          '<div class="pmc-aw-row-body"><div class="pmc-aw-row-title">' + sig.title + '</div><div class="pmc-aw-row-meta">' + sig.meta + '</div></div>' +
          '<div class="pmc-aw-row-num">' + sig.num + '</div>';
        return row;
      }

      function runScan(url) {
        stream.innerHTML = '';
        if (status) status.textContent = 'Scanning ' + url.replace(/^https?:\/\//, '').replace(/\/$/, '');
        aw.classList.add('scanning');
        var signals = buildSignals(url);
        var i = 0;
        function pushNext() {
          if (i >= signals.length) {
            aw.classList.remove('scanning');
            if (status) status.textContent = 'Audit complete';
            if (foot) foot.style.display = 'flex';
            return;
          }
          stream.appendChild(renderRow(signals[i]));
          requestAnimationFrame(function () {
            stream.children[stream.children.length - 1].classList.add('in');
          });
          i++;
          setTimeout(pushNext, 600);
        }
        if (foot) foot.style.display = 'none';
        setTimeout(pushNext, 400);
      }

      function trigger() {
        var url = normalizeUrl(input.value);
        if (!url) {
          input.classList.add('invalid');
          setTimeout(function () { input.classList.remove('invalid'); }, 1500);
          return;
        }
        runScan(url);
        // Capture the URL on the foot CTA so the contact form can pre-fill.
        var ftCta = aw.querySelector('.pmc-aw-foot-cta');
        if (ftCta) ftCta.href = '/start?audit_url=' + encodeURIComponent(url);
      }

      if (btn) btn.addEventListener('click', trigger);
      if (input) input.addEventListener('keydown', function (e) {
        if (e.key === 'Enter') { e.preventDefault(); trigger(); }
      });
    }
  });
})();
</script>
```

**Paste these into Page Settings → Custom Code via the proven CM6 paste workflow** (Joe will manually clear each editor first, then synthetic paste fits under the 11.5K cap for both).

## Section 21 - HTML Embed source codes (3 of them)

### Embed #1 - Hero Audit Widget

Drops on the canvas inside the right column of the hero. Wrapper id is `pmc-audit-widget` so the JS in body can target it.

```html
<div id="pmc-audit-widget" class="pmc-aw">
  <div class="pmc-aw-header">
    <div class="pmc-aw-dots"><span></span><span></span><span></span></div>
    <div class="pmc-aw-title">audit.pfendermarketing.com</div>
    <div class="pmc-aw-status"><span class="pmc-aw-live-dot"></span><span class="pmc-aw-status-text">Ready to scan</span></div>
  </div>
  <div class="pmc-aw-form">
    <input type="text" class="pmc-aw-input" placeholder="Paste your URL (e.g. yoursite.com)" autocomplete="off">
    <button type="button" class="pmc-aw-btn">Scan</button>
  </div>
  <div class="pmc-aw-stream"></div>
  <div class="pmc-aw-foot" style="display:none;">
    <span>Want the full audit?</span>
    <a href="/start" class="pmc-aw-foot-cta">Get it →</a>
  </div>
</div>
```

The styling for `.pmc-aw-*` classes is in the page head custom code. The JS for scanning is in page body. Joe just drops this block into the embed source.

### Embed #2 - Gauges grid

Drops inside the Opportunity section. Wrapper id is `pmc-gauges-embed`.

```html
<div id="pmc-gauges-embed" class="pmc-gauges">
  <div class="pmc-gauge" data-target="70">
    <div class="pmc-gauge-ring">
      <svg viewBox="0 0 130 130" width="130" height="130" aria-hidden="true">
        <circle class="track" cx="65" cy="65" r="56"></circle>
        <circle class="progress" cx="65" cy="65" r="56" stroke-dasharray="351.86" stroke-dashoffset="351.86"></circle>
      </svg>
      <span class="pmc-gauge-num">0%</span>
    </div>
    <span class="pmc-gauge-label">No call to action above the fold</span>
  </div>
  <div class="pmc-gauge" data-target="53">
    <div class="pmc-gauge-ring">
      <svg viewBox="0 0 130 130" width="130" height="130" aria-hidden="true">
        <circle class="track" cx="65" cy="65" r="56"></circle>
        <circle class="progress" cx="65" cy="65" r="56" stroke-dasharray="351.86" stroke-dashoffset="351.86"></circle>
      </svg>
      <span class="pmc-gauge-num">0%</span>
    </div>
    <span class="pmc-gauge-label">CTAs misaligned with the funnel</span>
  </div>
  <div class="pmc-gauge" data-target="72">
    <div class="pmc-gauge-ring">
      <svg viewBox="0 0 130 130" width="130" height="130" aria-hidden="true">
        <circle class="track" cx="65" cy="65" r="56"></circle>
        <circle class="progress" cx="65" cy="65" r="56" stroke-dasharray="351.86" stroke-dashoffset="351.86"></circle>
      </svg>
      <span class="pmc-gauge-num">0%</span>
    </div>
    <span class="pmc-gauge-label">No CTAs on interior pages</span>
  </div>
  <div class="pmc-gauge" data-target="68">
    <div class="pmc-gauge-ring">
      <svg viewBox="0 0 130 130" width="130" height="130" aria-hidden="true">
        <circle class="track" cx="65" cy="65" r="56"></circle>
        <circle class="progress" cx="65" cy="65" r="56" stroke-dasharray="351.86" stroke-dashoffset="351.86"></circle>
      </svg>
      <span class="pmc-gauge-num">0%</span>
    </div>
    <span class="pmc-gauge-label">No analytics or attribution wired</span>
  </div>
</div>
```

Add the gauge styles to page head CSS:

```css
.pmc-gauges { display: grid; grid-template-columns: repeat(4, 1fr); gap: 32px; max-width: 880px; margin: 0 auto; }
@media (max-width: 720px) { .pmc-gauges { grid-template-columns: repeat(2, 1fr); } }
.pmc-gauge { text-align: center; display: flex; flex-direction: column; align-items: center; gap: 14px; }
.pmc-gauge-ring { position: relative; width: 130px; height: 130px; display: flex; align-items: center; justify-content: center; }
.pmc-gauge-ring svg { transform: rotate(-90deg); }
.pmc-gauge-ring .track { fill: none; stroke: rgba(255,255,255,0.06); stroke-width: 8; }
.pmc-gauge-ring .progress { fill: none; stroke: #D7AF33; stroke-width: 8; stroke-linecap: round; transition: stroke-dashoffset 1500ms cubic-bezier(0.22, 1, 0.36, 1); filter: drop-shadow(0 0 10px rgba(215,175,51,0.30)); }
.pmc-gauge-num { position: absolute; inset: 0; display: flex; align-items: center; justify-content: center; font-family: 'Satoshi', sans-serif; font-weight: 700; font-size: 28px; color: #F5F0E8; letter-spacing: -0.02em; }
.pmc-gauge-label { font-size: 13px; color: rgba(245,240,232,0.7); line-height: 1.4; max-width: 150px; }
```

### Embed #3 - Audit Widget styling (lives in head custom code)

Add to the page head CSS section:

```css
.pmc-aw { position: relative; background: #0A1A18; border: 1px solid rgba(245,240,232,0.08); border-radius: 16px; overflow: hidden; box-shadow: 0 30px 60px -20px rgba(0,0,0,0.6); }
.pmc-aw-header { display: flex; align-items: center; justify-content: space-between; padding: 16px 20px; border-bottom: 1px solid rgba(245,240,232,0.08); background: rgba(255,255,255,0.02); }
.pmc-aw-dots { display: flex; gap: 6px; }
.pmc-aw-dots span { width: 9px; height: 9px; border-radius: 50%; background: rgba(245,240,232,0.18); }
.pmc-aw-title { font-family: 'Satoshi', sans-serif; font-weight: 600; font-size: 13px; color: rgba(245,240,232,0.7); }
.pmc-aw-status { display: flex; align-items: center; gap: 6px; font-size: 11px; color: #5A9E54; font-weight: 600; letter-spacing: 0.06em; text-transform: uppercase; }
.pmc-aw-live-dot { width: 7px; height: 7px; border-radius: 50%; background: #5A9E54; box-shadow: 0 0 6px rgba(90,158,84,0.6); }
.pmc-aw.scanning .pmc-aw-live-dot { animation: pmc-pulse 1.2s ease infinite; }
.pmc-aw-form { display: flex; gap: 8px; padding: 16px 20px; border-bottom: 1px solid rgba(245,240,232,0.04); }
.pmc-aw-input { flex: 1; background: rgba(255,255,255,0.04); border: 1px solid rgba(245,240,232,0.08); border-radius: 8px; padding: 10px 14px; color: #F5F0E8; font-family: inherit; font-size: 14px; outline: none; transition: border-color 200ms; }
.pmc-aw-input:focus { border-color: rgba(215,175,51,0.40); }
.pmc-aw-input.invalid { border-color: #d95252; }
.pmc-aw-input::placeholder { color: rgba(245,240,232,0.35); }
.pmc-aw-btn { padding: 10px 16px; border: 0; border-radius: 8px; background: #D7AF33; color: #040D0D; font-family: inherit; font-weight: 600; font-size: 13px; cursor: pointer; transition: background 200ms; }
.pmc-aw-btn:hover { background: #E5C044; }
.pmc-aw-stream { padding: 8px 20px 16px; min-height: 180px; }
.pmc-aw-row { display: flex; align-items: center; gap: 14px; padding: 12px 0; border-bottom: 1px solid rgba(245,240,232,0.04); opacity: 0; transform: translateY(8px); transition: opacity 400ms, transform 400ms; }
.pmc-aw-row:last-child { border-bottom: 0; }
.pmc-aw-row.in { opacity: 1; transform: translateY(0); }
.pmc-aw-row-icon { width: 32px; height: 32px; border-radius: 8px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
.pmc-aw-row.warn .pmc-aw-row-icon { background: rgba(215,175,51,0.10); color: #D7AF33; }
.pmc-aw-row.error .pmc-aw-row-icon { background: rgba(217,82,82,0.10); color: #d95252; }
.pmc-aw-row.ok .pmc-aw-row-icon { background: rgba(69,115,64,0.15); color: #5A9E54; }
.pmc-aw-row-icon svg { width: 16px; height: 16px; }
.pmc-aw-row-body { flex: 1; min-width: 0; }
.pmc-aw-row-title { font-size: 13px; color: #F5F0E8; font-weight: 500; line-height: 1.35; margin-bottom: 2px; }
.pmc-aw-row-meta { font-size: 11px; color: rgba(245,240,232,0.5); }
.pmc-aw-row-num { font-family: 'Satoshi', sans-serif; font-weight: 700; font-size: 18px; color: #F5F0E8; flex-shrink: 0; }
.pmc-aw-row.warn .pmc-aw-row-num { color: #D7AF33; }
.pmc-aw-row.error .pmc-aw-row-num { color: #d95252; }
.pmc-aw-foot { display: flex; align-items: center; justify-content: space-between; padding: 14px 20px; border-top: 1px solid rgba(245,240,232,0.08); background: rgba(255,255,255,0.015); font-size: 12px; color: rgba(245,240,232,0.5); }
.pmc-aw-foot-cta { color: #D7AF33; font-weight: 600; }
.pmc-aw-foot-cta:hover { color: #E5C044; text-decoration: underline; }
```

## Section 22 - Verification gates

After each section, save Designer and report. Joe approves before next section.

| Section | What to verify |
| --- | --- |
| Topbar | Renders at top, link works, dismiss x visible |
| Nav | Sticky, all 4 links route correctly, CTA in pill style |
| Hero | Two italic gold spans in H1, audit widget visible right side, scan button works (try one URL), CTAs render |
| Marquee | 4 client/product entries visible |
| Services | 4 cards 2x2 desktop, hover lift, no em dashes anywhere |
| Audit banner | Banner card centered, button works |
| Opportunity | 4 gauges fill on scroll into view |
| Process | 4 steps with gold numbers, no placeholder text |
| Why Pfender | Sticky aside left, portrait + paragraphs + italic pull quote right |
| Recent Work | HorseGrid live + Vitality faded, both with metrics + quotes |
| Tree CRM teaser | Screenshot right, pricing pills inline, CTA routes to /tree-crm |
| Contact | Trust list left, founder card right with email/phone/note |
| Footer | 4 cols including LineWeave AI mention, fine print row |

## Section 23 - Asset list (Joe to provide)

Mark with `[CONFIRM]` if Joe needs to provide. Defaults shown for placeholder.

- `pmc-icon.png` - PMC tree icon, 28x28 nav size, 56x56 footer size. Already in Drive folder.
- Joe portrait for Why Pfender section. `[CONFIRM photo]`. Use 4:5 aspect ratio, dark moody, no white background. Placeholder: solid card with PMC icon.
- Joe portrait round for Contact card. `[CONFIRM photo]`. 80x80 round.
- Tree CRM dashboard screenshot. Joe has 12 PNGs in Drive (`01-dashboard.png` through `12-automations.png`). Use `01-dashboard.png` for the Tree CRM teaser section. Upload to Webflow Asset Manager.
- HorseGrid Designer brand mark for marquee. Optional - text-only is fine.
- Vitality Wellness brand mark for marquee. Optional - text-only is fine.
- LineWeave AI brand mark for marquee. Optional - text-only is fine.
- OG image for the page. 1200x630. PMC brand. `[CONFIRM]` - can use the PMC icon centered on dark background as v1.

## Section 24 - Page metadata

Set in Page Settings → SEO and OG fields:

- SEO Title: "Pfender Marketing Co. - Marketing, software, and the operator who runs both"
- SEO Description: "Senior, AI-augmented marketing for founder-led businesses. Audits, SEO, paid, attribution, websites, brand. Plus Tree CRM, the operating system Joe built. Philadelphia, working nationwide."
- OG Title: same as SEO title
- OG Description: same as SEO description
- OG Image: see asset list

## Section 25 - When the build is done

After all 13 sections are built, verified, and saved:

1. Joe loads `https://pmc-staging.webflow.io/home-staging` in a new tab to view as a published page would render.
2. Joe runs through the page on desktop, tablet, mobile.
3. Joe spot-checks the audit widget by typing his own URL and pressing Scan.
4. Joe spot-checks the gauges by scrolling them into view.
5. Joe spot-checks the topbar dismiss + sessionStorage memo.
6. Joe spot-checks the sticky nav scroll state.
7. Joe spot-checks every link routes correctly.
8. **Do not publish.** Joe will decide separately when to remove the `/` redirect to `/start` and promote `/home-staging` to `/`.

## Section 26 - Failure modes and pickup notes

If a synthetic CM6 paste truncates: ask Joe to copy from VS Code and Cmd+V via real OS clipboard instead.

If Webflow MCP Designer connection drops mid-build: heartbeat with `variable_tool` and ask Joe to re-foreground the Designer tab.

If a section structure looks ambiguous in the prompt: build the simplest interpretation, save, ask Joe to react before assuming.

If a copy choice is unclear: use the version in the prompt verbatim. Do not "improve" it. Em dashes get converted to spaced hyphens silently. "Free" never appears.

If something doesn't fit Webflow Designer's element vocabulary cleanly: drop an HTML Embed only as a last resort, and only for the smallest possible subtree. Document why an embed was needed.

Pickup point if conversation context resets: this prompt + the build state of `/home-staging` (which sections are built, which aren't) is enough to resume from any point. The Variable IDs are stable and listed in Section 2.

---

End of prompt. Begin Section 3 (Pre-flight) when Joe says "begin build."
