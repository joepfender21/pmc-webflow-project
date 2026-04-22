# Tree CRM Iframe Embed Reference

## Standard Embed Template

All Tree CRM iframes on pfendermarketing.com use these exact attributes. There is **no Webflow wrapper card** around the iframe — Tree CRM's embed mode renders its own card. Adding an outer card wrapper causes a nested-card visual (two frames, mismatched shimmer lines). The iframe is the card.

```html
<iframe
  src="https://app.treecrm.co/{type}/{id}?embed=1"
  scrolling="no"
  allowtransparency="true"
  style="width: 100%; border: 0; display: block; margin: 0 auto;"
  loading="lazy"
  referrerpolicy="no-referrer-when-downgrade"
  title="{descriptive title}"
></iframe>
<p style="text-align: center; margin-top: 16px; font-size: 14px; color: rgba(245,240,232,0.5);">
  If the embedded form does not load,
  <a href="https://app.treecrm.co/{type}/{id}" target="_blank" rel="noopener noreferrer" style="color: #D7AF33;">fill it out here</a>.
</p>
```

If a wrapper div is needed for scroll-targeting or centering (e.g., `/case-studies` uses `id="csa-intake-form"` as a scroll anchor), make it a **bare positioning wrapper** — `max-width` and `margin: 0 auto` only, no `background`, `border`, `border-radius`, `box-shadow`, `padding`, or pseudo-element shimmer.

## Why `scrolling="no"`?

iOS Safari creates a separate scroll context for every iframe — even when the iframe is auto-sized to its full content height (so no inner scroll is needed). Safari still captures touch/swipe gestures within the iframe boundary and routes them to the iframe's scroll context instead of the parent page. On pages where the iframe is the primary content (`/start`, `/meeting`), this means users cannot scroll the page at all because every touch lands on the iframe.

`scrolling="no"` is a deprecated-but-still-supported HTML attribute that tells the browser not to create a scroll context for the iframe. With it set, iOS touch gestures pass through to the parent page. The iframe remains fully interactive — users can still tap inputs, dropdowns, and pill checkboxes. The form/booking logic inside the iframe is unaffected.

`/case-studies` did not exhibit this symptom because the page has substantial body content outside the iframe, giving users non-iframe areas to touch for scrolling. `/start` and `/meeting` do not.

## Why `allowtransparency="true"`?

CSS `background: transparent` alone is not consistently honored on iOS Safari for iframes. The `allowtransparency="true"` attribute is what iOS actually respects for transparent iframe backgrounds — without it, a white box renders behind the iframe content.

## Active Embeds

### /start — Discovery Intake Form
- Tree CRM URL: `https://app.treecrm.co/form/79098f68-4ef7-4849-9506-06d097c9ed37`
- Embed file: [pages/start/embeds/lead-form.html](../pages/start/embeds/lead-form.html)
- Pattern: bare iframe + fallback paragraph, no wrapper div

### /meeting — Discovery Call Booking
- Tree CRM URL: `https://app.treecrm.co/book/discovery-call`
- Embed file: [pages/meeting/embeds/scheduler.html](../pages/meeting/embeds/scheduler.html)
- Pattern: bare iframe + fallback paragraph, no wrapper div
- Page title ("Book a 30-Minute Intro Call") lives in the Webflow hero section above the embed, not in the embed HTML

### /case-studies — Tell Me About Your Business Form
- Tree CRM URL: `https://app.treecrm.co/form/be009c2c-c585-4ae7-a519-aac1dfc9b776`
- Embed file: [pages/case-studies/embeds/intake-form.html](../pages/case-studies/embeds/intake-form.html)
- Wrapper id: `#csa-intake-form` — scroll-to target in body.html (`pmcScrollToLeadForm`), do not remove

## Auto-Height Resize Listener

Registered once in **Site Settings > Footer** ([global/footer.html](../global/footer.html)) — runs on every page. Listens for `postMessage` events from Tree CRM iframes and resizes the matching iframe to fit its content.

- `tree-crm-resize` — sent by the booking widget
- `tree-crm-form:height` — sent by the form widget
- Matches by `e.source === f.contentWindow` so multiple iframes on the same page are handled independently
- Adds 20px to the reported height as a buffer against overflow

## Adding a New Tree CRM Embed

1. Get the full Tree CRM form or booking URL.
2. Copy the standard template above, replacing `{type}`, `{id}`, and `{title}`.
3. Only add a wrapper div if a scroll anchor or explicit centering target is needed (see `/case-studies` pattern above). If used, keep it a bare positioning wrapper only.
4. Paste into the Webflow embed element on the canvas. Embeds are HTML only — never add `<style>` or `<script>` tags inside.
5. The global auto-height listener in `global/footer.html` handles resizing automatically. No per-page JS is needed.
