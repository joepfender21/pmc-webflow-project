# PMC homepage build - Webflow canvas push

You are operating a Webflow Designer via the Chrome extension. The job is to wipe the existing 118 nodes on `/home-staging`, paste fresh head/body/embed code from Joe's local repo, and stop short of publishing.

## Hard rules

1. **No publish. No publish. No publish.** Staging or production - do not click Publish at any point. Save Designer state only.
2. **Do not touch any other page.** `/start`, `/meeting`, `/case-studies`, `/privacy`, `/terms`, `/404`, `/401` are sacred.
3. **Verify every paste.** Each CM6 paste must end at >= 95% of expected character length. If shorter, stop and report.
4. **Heartbeat first.** Before every Webflow MCP batch, run a tiny call (`mcp__Claude_in_Chrome__tabs_context_mcp`) to confirm the tab is foregrounded.
5. **Target CM6 editors by label text, never by index.** Webflow has multiple CM6 editors on the page (JSON-LD, head, footer, embeds) and order depends on what's expanded.

## Site context

- Webflow site: `https://pmc-staging.design.webflow.com`
- Site id: `698f6b2d83529eb0de880884`
- Custom domains: `pfendermarketing.com` and `www.pfendermarketing.com`
- Plan tier: paid (50KB embed limit)
- Page to update: **Home - Staging** (slug `/home-staging`, page id `69a0c1e3038ae1287aa80b16`)
- The page currently has 118 designer nodes built. They will be deleted in step 5.

## Procedure

### 0. Heartbeat + open the Designer

1. Navigate Chrome to `https://pmc-staging.design.webflow.com`
2. Wait for the Designer to fully load. Close any "MCP Bridge" or onboarding modals that appear in front of the canvas.
3. Switch to the **Home - Staging** page via the Pages panel on the left.

### 1. Open Page Settings and locate the Custom Code editors

1. In the Pages panel, hover **Home - Staging** and click the gear icon (Settings).
2. The Page Settings modal opens. Scroll the modal contents to the **Custom Code** section.
3. You will see two CM6 editors labeled exactly:
   - `Inside <head> tag`
   - `Before </body> tag`

### 2. Paste #1 - Inside <head> tag

Use the proven paste function below. It's the only path that works reliably with Webflow's CM6 editor. **Do not use `execCommand('insertText')` - it's unreliable on CM6.**

Run via `mcp__Claude_in_Chrome__javascript_tool`:

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
  cm.dispatchEvent(new KeyboardEvent('keydown', {
    key: 'a', ctrlKey: true, metaKey: true, bubbles: true
  }));
  await new Promise(r => setTimeout(r, 50));
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

// HEAD CSS payload follows. Joe will paste the contents of
// pmc-webflow-project/pages/home-draft/head.html here.
const HEAD_CSS = `<!-- ============================================================
     PMC HOMEPAGE - PAGE HEAD CSS (v2 - Satoshi only)
     Class system locked to mockup classes (.pmc-* scoped).
     All styles scoped under .pmc-home to avoid Webflow base bleed.
     ============================================================ -->

<link rel="preconnect" href="https://api.fontshare.com" crossorigin>
<link href="https://api.fontshare.com/v2/css?f[]=satoshi@400,500,700,400i,500i,700i&display=swap" rel="stylesheet">

<style>
  :root {
    --bg: #040D0D;
    --paper: #0A1A18;
    --ink: #F5F0E8;
    --ink-80: rgba(245,240,232,0.80);
    --ink-70: rgba(245,240,232,0.70);
    --ink-60: rgba(245,240,232,0.60);
    --ink-50: rgba(245,240,232,0.50);
    --ink-35: rgba(245,240,232,0.35);
    --gold: #D7AF33;
    --gold-bright: #E5C044;
    --gold-soft: rgba(215,175,51,0.12);
    --gold-line: rgba(215,175,51,0.20);
    --leaf: #457340;
    --leaf-bright: #5A9E54;
    --leaf-soft: rgba(69,115,64,0.15);
    --charcoal: #2A2A2A;
    --hairline: rgba(245,240,232,0.08);
    --hairline-soft: rgba(245,240,232,0.04);
    --card-bg: rgba(255,255,255,0.03);
    --card-bg-strong: rgba(255,255,255,0.05);
    --ease: cubic-bezier(0.22, 1, 0.36, 1);
    --max-w: 1080px;
    --max-edit: 720px;
    --pad-x: clamp(24px, 5vw, 64px);
  }

  /* Scope reset to avoid Webflow base style bleed */
  .pmc-home, .pmc-home * { box-sizing: border-box; }
  .pmc-home {
    font-family: 'Satoshi', system-ui, sans-serif;
    font-size: 17px; line-height: 1.65; color: var(--ink); background: var(--bg);
  }
  .pmc-home a { color: inherit; text-decoration: none; }
  .pmc-home button { font-family: inherit; cursor: pointer; border: 0; background: none; color: inherit; }
  .pmc-home img { max-width: 100%; display: block; }
  .pmc-home p { margin: 0; }
  .pmc-home h1, .pmc-home h2, .pmc-home h3, .pmc-home h4 { margin: 0; }
  .pmc-home ul { list-style: none; padding: 0; margin: 0; }

  /* TYPOGRAPHY */
  .pmc-home .display { font-family: 'Satoshi', sans-serif; font-weight: 700; letter-spacing: -0.02em; line-height: 1.04; }
  .pmc-home .display em { font-style: italic; font-weight: 700; color: var(--gold); }
  .pmc-home .h1 { font-size: clamp(38px, 6.4vw, 76px); }
  .pmc-home .h2 { font-size: clamp(30px, 4.4vw, 46px); line-height: 1.08; }
  .pmc-home .h3 { font-size: clamp(22px, 2.4vw, 28px); line-height: 1.18; }
  .pmc-home .h4 { font-family: 'Satoshi', sans-serif; font-weight: 700; font-size: 18px; letter-spacing: -0.01em; }
  .pmc-home .body { font-size: 17px; line-height: 1.7; color: var(--ink-80); }
  .pmc-home .body-sm { font-size: 15px; line-height: 1.65; color: var(--ink-70); }
  .pmc-home .body-xs { font-size: 13px; line-height: 1.55; color: var(--ink-60); }
  .pmc-home .eyebrow { display: inline-block; font-family: 'Satoshi', sans-serif; font-weight: 500; font-size: 12px; letter-spacing: 0.16em; text-transform: uppercase; color: var(--gold); }
  .pmc-home .editorial { max-width: var(--max-edit); }

  /* LAYOUT */
  .pmc-home .pmc-container { width: 100%; max-width: var(--max-w); margin: 0 auto; padding: 0 var(--pad-x); }
  .pmc-home .pmc-section { padding: clamp(72px, 10vh, 128px) 0; position: relative; }
  .pmc-home .pmc-section-tight { padding: clamp(48px, 7vh, 80px) 0; }

  /* TOPBAR */
  .pmc-topbar { background: var(--paper); border-bottom: 1px solid var(--hairline); padding: 10px var(--pad-x); display: flex; align-items: center; justify-content: center; gap: 12px; font-size: 13px; color: var(--ink-70); position: relative; transition: max-height 300ms var(--ease), padding 300ms var(--ease); overflow: hidden; max-height: 60px; }
  .pmc-topbar.dismissed { max-height: 0; padding-top: 0; padding-bottom: 0; border-bottom-width: 0; }
  .pmc-topbar-arrow { color: var(--ink-50); }
  .pmc-topbar-link { color: var(--gold); font-weight: 500; transition: color 200ms ease; }
  .pmc-topbar-link:hover { color: var(--gold-bright); text-decoration: underline; text-underline-offset: 3px; }
  .pmc-topbar-dismiss { position: absolute; right: 16px; top: 50%; transform: translateY(-50%); width: 22px; height: 22px; display: flex; align-items: center; justify-content: center; color: var(--ink-50); transition: color 200ms ease; font-size: 16px; }
  .pmc-topbar-dismiss:hover { color: var(--ink); }

  /* NAV */
  .pmc-nav { position: sticky; top: 0; z-index: 50; background: rgba(4,13,13,0.85); backdrop-filter: blur(12px); -webkit-backdrop-filter: blur(12px); border-bottom: 1px solid transparent; transition: border-color 200ms ease; }
  .pmc-nav.scrolled { border-bottom-color: var(--hairline); }
  .pmc-nav-inner { max-width: var(--max-w); margin: 0 auto; padding: 18px var(--pad-x); display: flex; align-items: center; justify-content: space-between; gap: 24px; }
  .pmc-nav-logo { display: flex; align-items: center; gap: 10px; font-family: 'Satoshi', sans-serif; font-weight: 700; font-size: 17px; letter-spacing: -0.01em; color: var(--ink); }
  .pmc-nav-logo-mark { width: 28px; height: 28px; border-radius: 6px; background: var(--bg); display: flex; align-items: center; justify-content: center; border: 1px solid var(--gold-line); flex-shrink: 0; }
  .pmc-nav-logo-mark svg { width: 18px; height: 18px; }
  .pmc-nav-links { display: flex; align-items: center; gap: 28px; }
  .pmc-nav-link { font-size: 14px; color: var(--ink-70); font-weight: 500; transition: color 200ms ease; }
  .pmc-nav-link:hover { color: var(--ink); }
  .pmc-nav-cta { padding: 10px 18px; border-radius: 999px; background: var(--ink); color: var(--bg); font-weight: 600; font-size: 14px; transition: transform 200ms var(--ease), background 200ms ease, color 200ms ease; }
  .pmc-nav-cta:hover { background: var(--gold); transform: translateY(-1px); }
  @media (max-width: 720px) { .pmc-nav-links { display: none; } }

  /* HERO */
  .pmc-hero { position: relative; overflow: hidden; padding: clamp(80px, 12vh, 160px) 0 clamp(64px, 10vh, 128px); }
  .pmc-hero::before, .pmc-hero::after { content: ""; position: absolute; inset: -2px; pointer-events: none; z-index: 0; opacity: 0; transition: opacity 500ms ease; }
  .pmc-hero::before { opacity: 0.55; background: radial-gradient(420px circle at var(--pmc-x, 30%) var(--pmc-y, 30%), rgba(215,175,51,0.10), rgba(69,115,64,0.07) 35%, rgba(0,0,0,0) 70%); mix-blend-mode: screen; }
  .pmc-hero::after { opacity: 0.65; background: radial-gradient(700px circle at 12% calc(20% + var(--pmc-s, 0px)), rgba(69,115,64,0.10), rgba(0,0,0,0) 60%), radial-gradient(760px circle at 88% calc(40% + var(--pmc-s, 0px)), rgba(215,175,51,0.08), rgba(0,0,0,0) 65%); filter: blur(10px); mix-blend-mode: screen; }
  .pmc-hero > * { position: relative; z-index: 1; }
  .pmc-hero-grid { display: grid; grid-template-columns: minmax(0, 1.15fr) minmax(0, 1fr); gap: 72px; align-items: center; }
  @media (max-width: 1000px) { .pmc-hero-grid { grid-template-columns: 1fr; gap: 56px; } }
  .pmc-hero-eyebrow { display: inline-flex; align-items: center; gap: 10px; margin-bottom: 28px; color: var(--ink-70); font-size: 13px; }
  .pmc-hero-eyebrow .dot { width: 8px; height: 8px; border-radius: 50%; background: var(--leaf-bright); box-shadow: 0 0 0 3px rgba(90,158,84,0.18); animation: pmc-pulse 2.4s var(--ease) infinite; }
  @keyframes pmc-pulse { 0%,100% { box-shadow: 0 0 0 0 rgba(90,158,84,0.40); } 50% { box-shadow: 0 0 0 6px rgba(90,158,84,0.0); } }
  .pmc-hero-h1 { margin-bottom: 28px; }
  .pmc-hero-sub { font-size: clamp(18px, 1.55vw, 21px); line-height: 1.55; color: var(--ink-80); max-width: 540px; margin-bottom: 40px; }
  .pmc-hero-ctas { display: flex; flex-wrap: wrap; gap: 12px; margin-bottom: 48px; }

  /* BUTTONS */
  .pmc-btn { display: inline-flex; align-items: center; gap: 10px; padding: 14px 22px; border-radius: 8px; font-family: 'Satoshi', sans-serif; font-weight: 600; font-size: 15px; letter-spacing: 0.01em; transition: transform 200ms var(--ease), background 200ms ease, color 200ms ease, border-color 200ms ease; cursor: pointer; }
  .pmc-btn-primary { background: var(--ink); color: var(--bg); border: 0; }
  .pmc-btn-primary:hover { background: var(--gold); transform: translateY(-1px); }
  .pmc-btn-secondary { background: transparent; color: var(--ink); border: 1px solid var(--hairline); }
  .pmc-btn-secondary:hover { border-color: var(--gold-line); color: var(--gold); }
  .pmc-btn svg { transition: transform 200ms var(--ease); }
  .pmc-btn:hover svg { transform: translateX(3px); }

  /* CRED STRIP */
  .pmc-hero-creds { display: grid; grid-template-columns: repeat(3, 1fr); gap: 24px; padding-top: 28px; border-top: 1px solid var(--hairline); max-width: 600px; }
  @media (max-width: 600px) { .pmc-hero-creds { grid-template-columns: 1fr; gap: 16px; } }
  .pmc-cred-num { font-family: 'Satoshi', sans-serif; font-weight: 700; font-size: 22px; color: var(--gold); margin-bottom: 4px; letter-spacing: -0.02em; }
  .pmc-cred-label { font-size: 13px; color: var(--ink-60); line-height: 1.4; }

  /* HERO AUDIT WIDGET */
  .pmc-aw { position: relative; background: var(--paper); border: 1px solid var(--hairline); border-radius: 16px; overflow: hidden; box-shadow: 0 30px 60px -20px rgba(0,0,0,0.6), 0 0 0 1px var(--hairline-soft); }
  .pmc-aw::before { content: ""; position: absolute; top: 0; left: 0; right: 0; height: 1px; background: linear-gradient(90deg, transparent 0%, rgba(215,175,51,0.20) 30%, rgba(215,175,51,0.95) 50%, rgba(215,175,51,0.20) 70%, transparent 100%); opacity: 0; transform: translateX(-30%); pointer-events: none; z-index: 3; filter: drop-shadow(0 0 10px rgba(215,175,51,0.30)); }
  .pmc-aw.shimmer-active::before { opacity: 0.9; animation: pmc-aw-shimmer 3.2s linear infinite; }
  @keyframes pmc-aw-shimmer { 0% { transform: translateX(-30%); } 100% { transform: translateX(30%); } }
  .pmc-aw-header { display: flex; align-items: center; justify-content: space-between; padding: 16px 20px; border-bottom: 1px solid var(--hairline); background: rgba(255,255,255,0.02); }
  .pmc-aw-dots { display: flex; gap: 6px; }
  .pmc-aw-dots span { width: 9px; height: 9px; border-radius: 50%; background: rgba(245,240,232,0.18); }
  .pmc-aw-title { font-family: 'Satoshi', sans-serif; font-weight: 600; font-size: 13px; color: var(--ink-70); letter-spacing: 0.02em; }
  .pmc-aw-status { display: flex; align-items: center; gap: 6px; font-size: 11px; color: var(--leaf-bright); font-weight: 600; letter-spacing: 0.06em; text-transform: uppercase; }
  .pmc-aw-status .live-dot { width: 7px; height: 7px; border-radius: 50%; background: var(--leaf-bright); box-shadow: 0 0 6px rgba(90,158,84,0.6); animation: pmc-pulse 1.8s var(--ease) infinite; }
  .pmc-aw-stream { padding: 8px 20px 20px; min-height: 280px; }
  .pmc-aw-row { display: flex; align-items: center; gap: 14px; padding: 14px 0; border-bottom: 1px solid var(--hairline-soft); opacity: 0; transform: translateY(8px); transition: opacity 400ms var(--ease), transform 400ms var(--ease); }
  .pmc-aw-row:last-child { border-bottom: 0; }
  .pmc-aw-row.in { opacity: 1; transform: translateY(0); }
  .pmc-aw-row-icon { width: 32px; height: 32px; border-radius: 8px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
  .pmc-aw-row.warn .pmc-aw-row-icon { background: rgba(215,175,51,0.10); color: var(--gold); }
  .pmc-aw-row.error .pmc-aw-row-icon { background: rgba(217,82,82,0.10); color: #d95252; }
  .pmc-aw-row.ok .pmc-aw-row-icon { background: var(--leaf-soft); color: var(--leaf-bright); }
  .pmc-aw-row-icon svg { width: 16px; height: 16px; }
  .pmc-aw-row-body { flex: 1; min-width: 0; }
  .pmc-aw-row-title { font-size: 13px; color: var(--ink); font-weight: 500; line-height: 1.35; margin-bottom: 2px; }
  .pmc-aw-row-meta { font-size: 11px; color: var(--ink-50); letter-spacing: 0.02em; }
  .pmc-aw-row-num { font-family: 'Satoshi', sans-serif; font-weight: 700; font-size: 18px; color: var(--ink); letter-spacing: -0.02em; flex-shrink: 0; }
  .pmc-aw-row.warn .pmc-aw-row-num { color: var(--gold); }
  .pmc-aw-row.error .pmc-aw-row-num { color: #d95252; }
  .pmc-aw-foot { display: flex; align-items: center; justify-content: space-between; padding: 14px 20px; border-top: 1px solid var(--hairline); background: rgba(255,255,255,0.015); font-size: 12px; color: var(--ink-50); }
  .pmc-aw-foot-cta { color: var(--gold); font-weight: 600; }
  .pmc-aw-foot-cta:hover { color: var(--gold-bright); text-decoration: underline; text-underline-offset: 2px; }

  /* SERVICES */
  .pmc-services-head { display: flex; align-items: end; justify-content: space-between; gap: 32px; margin-bottom: 56px; }
  @media (max-width: 720px) { .pmc-services-head { flex-direction: column; align-items: flex-start; } }
  .pmc-services-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 16px; }
  @media (max-width: 720px) { .pmc-services-grid { grid-template-columns: 1fr; } }
  .pmc-service-card { position: relative; padding: 32px; background: var(--card-bg); border: 1px solid var(--hairline); border-radius: 14px; transition: border-color 300ms ease, transform 300ms var(--ease), background 300ms ease; }
  .pmc-service-card:hover { border-color: rgba(215,175,51,0.25); background: var(--card-bg-strong); transform: translateY(-3px); }
  .pmc-service-card .h4 { margin-bottom: 8px; }
  .pmc-service-card .body-sm { color: var(--ink-70); margin-bottom: 20px; }
  .pmc-service-tags { display: flex; flex-wrap: wrap; gap: 6px; padding-top: 16px; border-top: 1px solid var(--hairline-soft); }
  .pmc-service-tag { font-size: 11px; color: var(--ink-60); font-weight: 500; padding: 4px 10px; border-radius: 999px; background: var(--hairline-soft); letter-spacing: 0.02em; }
  .pmc-link-arrow { display: inline-flex; align-items: center; gap: 8px; margin-top: 32px; color: var(--gold); font-weight: 600; font-size: 15px; transition: color 200ms ease; }
  .pmc-link-arrow:hover { color: var(--gold-bright); }
  .pmc-link-arrow svg { transition: transform 200ms var(--ease); }
  .pmc-link-arrow:hover svg { transform: translateX(4px); }

  /* AUDIT BANNER */
  .pmc-audit-banner { margin: 0 auto; background: linear-gradient(135deg, rgba(215,175,51,0.06), rgba(69,115,64,0.06)); border: 1px solid var(--gold-line); border-radius: 18px; padding: 36px 40px; display: flex; align-items: center; justify-content: space-between; gap: 32px; transition: border-color 300ms ease, transform 300ms var(--ease); }
  .pmc-audit-banner:hover { border-color: rgba(215,175,51,0.40); transform: translateY(-2px); }
  .pmc-audit-banner-left { display: flex; align-items: center; gap: 20px; }
  .pmc-audit-banner-icon { width: 52px; height: 52px; border-radius: 12px; display: flex; align-items: center; justify-content: center; background: rgba(69,115,64,0.18); flex-shrink: 0; }
  .pmc-audit-banner-icon svg { width: 24px; height: 24px; color: var(--leaf-bright); }
  .pmc-audit-banner-headline { font-size: 20px; font-weight: 700; color: var(--ink); margin-bottom: 4px; font-family: 'Satoshi', sans-serif; letter-spacing: -0.01em; }
  .pmc-audit-banner-sub { font-size: 14px; color: var(--ink-70); }
  .pmc-audit-banner-btn { display: inline-flex; align-items: center; gap: 8px; padding: 12px 20px; border-radius: 999px; background: var(--gold); color: var(--bg); border: 0; font-family: 'Satoshi', sans-serif; font-weight: 600; font-size: 14px; transition: background 200ms ease, transform 200ms var(--ease); flex-shrink: 0; cursor: pointer; }
  .pmc-audit-banner-btn:hover { background: var(--gold-bright); transform: translateY(-1px); }
  @media (max-width: 720px) { .pmc-audit-banner { flex-direction: column; align-items: flex-start; padding: 28px; } .pmc-audit-banner-btn { width: 100%; justify-content: center; } }

  /* OPPORTUNITY GAUGES */
  .pmc-opportunity { background: var(--paper); border-top: 1px solid var(--hairline); border-bottom: 1px solid var(--hairline); }
  .pmc-opportunity-head { margin-bottom: 56px; }
  .pmc-gauges { display: grid; grid-template-columns: repeat(4, 1fr); gap: 32px; max-width: 880px; margin: 0 auto; }
  @media (max-width: 720px) { .pmc-gauges { grid-template-columns: repeat(2, 1fr); } }
  .pmc-gauge { text-align: center; display: flex; flex-direction: column; align-items: center; gap: 14px; }
  .pmc-gauge-ring { position: relative; width: 130px; height: 130px; display: flex; align-items: center; justify-content: center; }
  .pmc-gauge-ring svg { transform: rotate(-90deg); }
  .pmc-gauge-ring .track { fill: none; stroke: rgba(255,255,255,0.06); stroke-width: 8; }
  .pmc-gauge-ring .progress { fill: none; stroke: var(--gold); stroke-width: 8; stroke-linecap: round; transition: stroke-dashoffset 1500ms var(--ease); filter: drop-shadow(0 0 10px rgba(215,175,51,0.30)); }
  .pmc-gauge-num { position: absolute; inset: 0; display: flex; align-items: center; justify-content: center; font-family: 'Satoshi', sans-serif; font-weight: 700; font-size: 28px; color: var(--ink); letter-spacing: -0.02em; }
  .pmc-gauge-label { font-size: 13px; color: var(--ink-70); line-height: 1.4; max-width: 150px; }
  .pmc-opportunity-foot { display: flex; justify-content: center; gap: 8px; margin-top: 56px; font-size: 14px; color: var(--ink-60); }
  .pmc-opportunity-foot a { color: var(--gold); font-weight: 600; }
  .pmc-opportunity-foot a:hover { color: var(--gold-bright); text-decoration: underline; text-underline-offset: 2px; }

  /* PROCESS */
  .pmc-process-head { margin-bottom: 64px; }
  .pmc-process-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 24px; }
  @media (max-width: 900px) { .pmc-process-grid { grid-template-columns: repeat(2, 1fr); } }
  @media (max-width: 540px) { .pmc-process-grid { grid-template-columns: 1fr; } }
  .pmc-step { padding-top: 16px; border-top: 1px solid var(--gold-line); }
  .pmc-step-num { display: block; font-family: 'Satoshi', sans-serif; font-weight: 700; font-size: 13px; color: var(--gold); letter-spacing: 0.06em; margin-bottom: 12px; }
  .pmc-step-title { font-family: 'Satoshi', sans-serif; font-weight: 700; font-size: 19px; color: var(--ink); margin-bottom: 8px; letter-spacing: -0.01em; line-height: 1.2; }
  .pmc-step-body { font-size: 14px; line-height: 1.6; color: var(--ink-70); margin: 0; }

  /* WHY PFENDER */
  .pmc-why-grid { display: grid; grid-template-columns: minmax(0, 0.8fr) minmax(0, 1fr); gap: 80px; align-items: start; }
  @media (max-width: 900px) { .pmc-why-grid { grid-template-columns: 1fr; gap: 40px; } }
  .pmc-why-aside { position: sticky; top: 100px; padding-top: 4px; }
  .pmc-why-aside .h2 { margin-bottom: 16px; }
  .pmc-why-aside .body { color: var(--ink-70); }
  .pmc-why-prose .body + .body { margin-top: 20px; }
  .pmc-why-prose .pull { display: block; font-family: 'Satoshi', sans-serif; font-style: italic; font-weight: 500; font-size: clamp(22px, 2.2vw, 28px); line-height: 1.3; color: var(--ink); margin: 32px 0; padding-left: 20px; border-left: 2px solid var(--gold); letter-spacing: -0.01em; }

  /* WORK */
  .pmc-work { background: var(--paper); border-top: 1px solid var(--hairline); border-bottom: 1px solid var(--hairline); }
  .pmc-work-head { display: flex; align-items: end; justify-content: space-between; gap: 32px; margin-bottom: 56px; }
  @media (max-width: 720px) { .pmc-work-head { flex-direction: column; align-items: flex-start; } }
  .pmc-work-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 24px; }
  @media (max-width: 900px) { .pmc-work-grid { grid-template-columns: 1fr; } }
  .pmc-work-card { position: relative; padding: 36px; background: var(--card-bg); border: 1px solid var(--hairline); border-radius: 16px; display: flex; flex-direction: column; gap: 24px; transition: border-color 300ms ease, transform 300ms var(--ease); }
  .pmc-work-card:hover { border-color: rgba(215,175,51,0.25); transform: translateY(-3px); }
  .pmc-work-card.coming-soon { opacity: 0.55; }
  .pmc-work-meta { display: flex; align-items: center; gap: 12px; font-size: 12px; color: var(--ink-60); font-weight: 500; letter-spacing: 0.06em; text-transform: uppercase; }
  .pmc-work-meta .pill { padding: 3px 10px; border-radius: 999px; background: var(--leaf-soft); color: var(--leaf-bright); font-size: 11px; }
  .pmc-work-meta .pill.queued { background: var(--gold-soft); color: var(--gold); }
  .pmc-work-title { font-family: 'Satoshi', sans-serif; font-weight: 700; font-size: clamp(22px, 2vw, 26px); line-height: 1.18; color: var(--ink); letter-spacing: -0.01em; }
  .pmc-work-blurb { font-size: 14px; line-height: 1.6; color: var(--ink-70); }
  .pmc-metrics { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; padding-top: 24px; border-top: 1px solid var(--hairline-soft); }
  .pmc-metric-label { font-size: 10px; color: var(--ink-50); font-weight: 500; letter-spacing: 0.10em; text-transform: uppercase; margin-bottom: 6px; }
  .pmc-metric-num { font-family: 'Satoshi', sans-serif; font-weight: 700; font-size: 26px; color: var(--gold); letter-spacing: -0.02em; line-height: 1; margin-bottom: 4px; }
  .pmc-metric-context { font-size: 11px; color: var(--ink-60); }
  .pmc-work-quote { margin-top: 8px; padding: 18px 20px; background: var(--hairline-soft); border-radius: 10px; border-left: 2px solid var(--gold); }
  .pmc-work-quote-text { font-family: 'Satoshi', sans-serif; font-weight: 500; font-size: 15px; line-height: 1.4; font-style: italic; color: var(--ink); margin-bottom: 6px; }
  .pmc-work-quote-author { font-size: 12px; color: var(--ink-60); }
  .pmc-work-link { display: inline-flex; align-items: center; gap: 6px; margin-top: 4px; align-self: flex-start; color: var(--gold); font-weight: 600; font-size: 14px; transition: color 200ms ease; }
  .pmc-work-link:hover { color: var(--gold-bright); }
  .pmc-work-link svg { transition: transform 200ms var(--ease); }
  .pmc-work-link:hover svg { transform: translateX(3px); }

  /* CONTACT */
  .pmc-contact-head { margin-bottom: 32px; }
  .pmc-contact-grid { display: grid; grid-template-columns: minmax(0, 1.05fr) minmax(0, 1fr); gap: 64px; align-items: start; }
  @media (max-width: 900px) { .pmc-contact-grid { grid-template-columns: 1fr; gap: 40px; } }
  .pmc-contact-trust { display: flex; flex-direction: column; gap: 18px; margin-top: 32px; }
  .pmc-trust-row { display: flex; align-items: center; gap: 12px; }
  .pmc-trust-check { width: 22px; height: 22px; border-radius: 50%; background: rgba(69,115,64,0.20); display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
  .pmc-trust-check svg { width: 12px; height: 12px; color: var(--leaf-bright); }
  .pmc-trust-text { font-size: 14px; color: var(--ink-80); }
  .pmc-contact-card { background: var(--paper); border: 1px solid var(--hairline); border-radius: 16px; padding: 28px; display: flex; flex-direction: column; gap: 16px; }
  .pmc-contact-card-row { display: flex; align-items: flex-start; gap: 14px; padding: 14px 0; border-bottom: 1px solid var(--hairline-soft); }
  .pmc-contact-card-row:last-of-type { border-bottom: 0; }
  .pmc-contact-card-row .label { font-size: 11px; letter-spacing: 0.08em; text-transform: uppercase; color: var(--ink-50); font-weight: 500; min-width: 88px; padding-top: 2px; }
  .pmc-contact-card-row .value { font-size: 15px; color: var(--ink); }
  .pmc-contact-card-row .value a { color: var(--ink); border-bottom: 1px solid var(--gold-line); padding-bottom: 1px; transition: color 200ms ease, border-color 200ms ease; }
  .pmc-contact-card-row .value a:hover { color: var(--gold); border-bottom-color: var(--gold); }
  .pmc-contact-card-actions { display: flex; flex-direction: column; gap: 10px; margin-top: 8px; }
  .pmc-contact-card-actions .pmc-btn { justify-content: center; }

  /* FOOTER */
  .pmc-footer { border-top: 1px solid var(--hairline); padding: 64px 0 40px; background: var(--bg); }
  .pmc-footer-grid { display: grid; grid-template-columns: 1.4fr 1fr 1fr 1fr; gap: 48px; margin-bottom: 56px; }
  @media (max-width: 720px) { .pmc-footer-grid { grid-template-columns: 1fr 1fr; gap: 32px; } }
  .pmc-footer-brand .pmc-nav-logo { margin-bottom: 16px; }
  .pmc-footer-blurb { font-size: 14px; color: var(--ink-60); line-height: 1.6; max-width: 280px; }
  .pmc-footer-col-title { font-size: 12px; color: var(--ink-50); font-weight: 500; letter-spacing: 0.10em; text-transform: uppercase; margin-bottom: 16px; }
  .pmc-footer-col ul { display: flex; flex-direction: column; gap: 10px; }
  .pmc-footer-col a { font-size: 14px; color: var(--ink-80); transition: color 200ms ease; }
  .pmc-footer-col a:hover { color: var(--gold); }
  .pmc-footer-fine { display: flex; align-items: center; justify-content: space-between; gap: 16px; padding-top: 32px; border-top: 1px solid var(--hairline); font-size: 12px; color: var(--ink-50); }
  @media (max-width: 540px) { .pmc-footer-fine { flex-direction: column; align-items: flex-start; } }
  .pmc-footer-fine a { color: var(--ink-60); }
  .pmc-footer-fine a:hover { color: var(--gold); }
  .pmc-footer-legal-links { display: flex; gap: 24px; }

  /* REDUCED MOTION */
  @media (prefers-reduced-motion: reduce) {
    .pmc-home *,
    .pmc-home *::before,
    .pmc-home *::after { animation: none !important; transition: none !important; }
  }
</style>`;

const result = await pasteIntoCMEditor("Inside <head> tag", HEAD_CSS);
return result;
```

Expected return: `{ ok: true, insertedLen: ~25092, expectedLen: 25092 }`. If `ok: false`, stop and report. Do not retry blindly - re-read the modal state first.

### 3. Paste #2 - Before </body> tag

Same procedure, different target label and payload.

```javascript
const FOOTER_JS = `<script>
(function () {
  if (window.__pmc_home_page) return;
  window.__pmc_home_page = true;
  try { if (window.Webflow && Webflow.env && Webflow.env('design')) return; } catch (e) {}

  document.addEventListener('DOMContentLoaded', function () {
    var reduce = window.matchMedia && window.matchMedia('(prefers-reduced-motion: reduce)').matches;

    /* TOPBAR DISMISS (sessionStorage memo) */
    var topbar = document.querySelector('.pmc-topbar');
    if (topbar) {
      if (sessionStorage.getItem('pmc_topbar_dismissed')) topbar.classList.add('dismissed');
      var topbarBtn = topbar.querySelector('.pmc-topbar-dismiss');
      if (topbarBtn) topbarBtn.addEventListener('click', function () {
        topbar.classList.add('dismissed');
        sessionStorage.setItem('pmc_topbar_dismissed', 'true');
      });
    }

    /* NAV SCROLL STATE */
    var nav = document.querySelector('.pmc-nav');
    function navState() { if (!nav) return; if (window.scrollY > 8) nav.classList.add('scrolled'); else nav.classList.remove('scrolled'); }
    window.addEventListener('scroll', navState, { passive: true });
    navState();

    /* HERO SPOTLIGHT + AURORA (mouse + scroll, RAF-throttled, cached rect) */
    if (!reduce) {
      var hero = document.querySelector('.pmc-hero');
      if (hero) {
        var heroRect = hero.getBoundingClientRect();
        var pendingMove = false, pendingScroll = false;
        var mouse = { x: 0, y: 0 };
        function refreshHeroRect() { heroRect = hero.getBoundingClientRect(); }
        window.addEventListener('resize', refreshHeroRect, { passive: true });
        window.addEventListener('scroll', refreshHeroRect, { passive: true });
        hero.addEventListener('mousemove', function (e) {
          mouse.x = e.clientX; mouse.y = e.clientY;
          if (pendingMove) return;
          pendingMove = true;
          requestAnimationFrame(function () {
            pendingMove = false;
            var x = ((mouse.x - heroRect.left) / heroRect.width) * 100;
            var y = ((mouse.y - heroRect.top) / heroRect.height) * 100;
            hero.style.setProperty('--pmc-x', x + '%');
            hero.style.setProperty('--pmc-y', y + '%');
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

    /* AUDIT WIDGET STREAM */
    var streamEl = document.querySelector('#pmcAuditStream');
    var FINDINGS = [
      { type: 'error', icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/></svg>', title: 'No call to action above the fold', meta: 'homepage / hero', num: '47' },
      { type: 'warn',  icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M10.29 3.86 1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"/><line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/></svg>', title: 'Ad budget waste detected', meta: 'paid / non-converting clusters', num: '23%' },
      { type: 'warn',  icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 3h18l-7 8v6l-4 2v-8L3 3z"/></svg>', title: 'Attribution gaps in funnel', meta: 'GA4 / GTM coverage', num: '12' },
      { type: 'error', icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="13 2 3 14 12 14 11 22 21 10 12 10 13 2"/></svg>', title: 'Conversion drop-off on /pricing', meta: 'session recording / scroll', num: '8' },
      { type: 'warn',  icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 11.5a8.38 8.38 0 0 1-.9 3.8 8.5 8.5 0 0 1-7.6 4.7 8.38 8.38 0 0 1-3.8-.9L3 21l1.9-5.7a8.38 8.38 0 0 1-.9-3.8 8.5 8.5 0 0 1 4.7-7.6 8.38 8.38 0 0 1 3.8-.9h.5a8.48 8.48 0 0 1 8 8v.5z"/></svg>', title: 'Slow lead response time', meta: 'first reply / business hours', num: '4.2h' },
      { type: 'ok',    icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"/></svg>', title: 'OG image and meta tags clean', meta: 'social preview', num: 'OK' },
      { type: 'error', icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><line x1="15" y1="9" x2="9" y2="15"/><line x1="9" y1="9" x2="15" y2="15"/></svg>', title: 'Schema markup missing', meta: 'organization / product / faq', num: '0' },
      { type: 'warn',  icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="11" cy="11" r="8"/><line x1="21" y1="21" x2="16.65" y2="16.65"/></svg>', title: 'Local SEO opportunity', meta: 'GBP signal coverage', num: '+38%' },
      { type: 'warn',  icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"/></svg>', title: 'No call tracking on inbound', meta: 'phone / form attribution', num: '0/4' }
    ];
    function makeRow(f) {
      var row = document.createElement('div');
      row.className = 'pmc-aw-row ' + f.type;
      row.innerHTML =
        '<div class="pmc-aw-row-icon">' + f.icon + '</div>' +
        '<div class="pmc-aw-row-body"><div class="pmc-aw-row-title">' + f.title + '</div><div class="pmc-aw-row-meta">' + f.meta + '</div></div>' +
        '<div class="pmc-aw-row-num">' + f.num + '</div>';
      return row;
    }
    if (streamEl) {
      var idx = 0;
      function pushNext() {
        var f = FINDINGS[idx % FINDINGS.length];
        var row = makeRow(f);
        streamEl.appendChild(row);
        requestAnimationFrame(function () { row.classList.add('in'); });
        while (streamEl.children.length > 5) {
          var first = streamEl.children[0];
          first.style.transition = 'opacity 350ms ease, max-height 350ms ease, margin 350ms ease, padding 350ms ease';
          first.style.opacity = '0';
          first.style.maxHeight = '0';
          first.style.margin = '0';
          first.style.padding = '0';
          first.style.borderBottomWidth = '0';
          (function (el) { setTimeout(function () { if (el && el.parentNode) el.parentNode.removeChild(el); }, 360); })(first);
          break;
        }
        idx++;
      }
      [0,1,2,3].forEach(function () { pushNext(); });
      if (!reduce) setInterval(pushNext, 2400);
    }

    /* GAUGE FILL ON SCROLL-INTO-VIEW */
    var CIRC = 351.86; /* 2 * pi * 56 */
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

    /* SMOOTH SCROLL FOR ANCHOR LINKS */
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
  });
})();
</script>`;
const result = await pasteIntoCMEditor("Before </body> tag", FOOTER_JS);
return result;
```

Expected return: `insertedLen: ~9161`.

### 4. Save the Page Settings modal

1. Click **Save Changes** at the bottom of the Page Settings modal.
2. Wait for the save confirmation. Close the modal.

### 5. Wipe the existing canvas content

The /home-staging page has 118 nodes inherited from the older draft. Delete all of them so the page is empty.

1. With Home - Staging selected, the canvas now shows the existing layout.
2. In the Navigator panel (left side, layers icon), find the top-level Body element.
3. Expand the Body. Select EVERY direct child (topbar, nav, all sections, footer).
4. Either right-click and "Delete" each, or select all children and press Delete.
5. After deletion, the Body should contain zero children. The canvas is empty.

If the Navigator approach is fragile, an alternative: select children one at a time on the canvas itself and press Delete. Whatever works.

### 6. Drop the new Body markup

1. The Body is now empty.
2. Open the Add panel (the `+` icon top-left).
3. Drag a single **Embed** element onto the empty Body. The Embed editor modal opens.
4. The Embed editor is also a CM6 editor. The active CM6 in the modal is your target.

Run:

```javascript
const BODY_HTML = `<!-- ============================================================
     PMC HOMEPAGE - BODY MARKUP
     Wrap entire page in .pmc-home scope. CSS in page head,
     JS in page footer, this is HTML-only.
     ============================================================ -->

<div class="pmc-home">

  <!-- TOPBAR -->
  <div class="pmc-topbar">
    <span>Founding client spots are limited</span>
    <span class="pmc-topbar-arrow">&rarr;</span>
    <a href="#contact" class="pmc-topbar-link">Apply now</a>
    <button class="pmc-topbar-dismiss" type="button" aria-label="Dismiss">&times;</button>
  </div>

  <!-- NAV -->
  <header class="pmc-nav">
    <div class="pmc-nav-inner">
      <a href="#" class="pmc-nav-logo">
        <span class="pmc-nav-logo-mark">
          <svg viewBox="0 0 24 24" fill="none" aria-hidden="true">
            <path d="M5 18 L9 14 L7 12 L11 8 L13 10 L17 6" stroke="#457340" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
            <rect x="6" y="16" width="2" height="3" fill="#D7AF33"/>
            <rect x="10" y="14" width="2" height="5" fill="#D7AF33"/>
            <rect x="14" y="10" width="2" height="9" fill="#D7AF33"/>
          </svg>
        </span>
        Pfender Marketing Co.
      </a>
      <nav class="pmc-nav-links" aria-label="Primary">
        <a href="#services" class="pmc-nav-link">Services</a>
        <a href="#work" class="pmc-nav-link">Work</a>
        <a href="#about" class="pmc-nav-link">About</a>
        <a href="/tree-crm" class="pmc-nav-link">Tree CRM</a>
      </nav>
      <a href="#contact" class="pmc-nav-cta">Get an audit</a>
    </div>
  </header>

  <!-- HERO -->
  <section class="pmc-hero">
    <div class="pmc-container pmc-hero-grid">
      <div>
        <div class="pmc-hero-eyebrow">
          <span class="dot"></span>
          <span>Philadelphia &middot; working nationwide</span>
        </div>
        <h1 class="display h1 pmc-hero-h1">
          Full-stack marketing<br>
          for <em>founders.</em>
        </h1>
        <p class="pmc-hero-sub">
          Senior, AI-augmented, run by one operator. No agency stack to manage, no junior handoffs, no playbook you've already seen. Audits, SEO, paid media, attribution, websites, brand - the whole funnel, owned end to end.
        </p>
        <div class="pmc-hero-ctas">
          <a href="#contact" class="pmc-btn pmc-btn-primary">
            Get an audit
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M5 12h14M12 5l7 7-7 7"/></svg>
          </a>
          <a href="#work" class="pmc-btn pmc-btn-secondary">
            See the work
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M9 5l7 7-7 7"/></svg>
          </a>
        </div>
        <div class="pmc-hero-creds">
          <div>
            <div class="pmc-cred-num">10+</div>
            <div class="pmc-cred-label">Years inside agencies, in-house, and the funnel</div>
          </div>
          <div>
            <div class="pmc-cred-num">1</div>
            <div class="pmc-cred-label">Operator. You work directly with me, every step</div>
          </div>
          <div>
            <div class="pmc-cred-num">&infin;</div>
            <div class="pmc-cred-label">AI surface. Built into the workflow, not bolted on</div>
          </div>
        </div>
      </div>

      <!-- AUDIT PREVIEW WIDGET -->
      <div class="pmc-aw shimmer-active" aria-label="Live audit preview">
        <div class="pmc-aw-header">
          <div class="pmc-aw-dots"><span></span><span></span><span></span></div>
          <div class="pmc-aw-title">audit.pfendermarketing.com</div>
          <div class="pmc-aw-status"><span class="live-dot"></span>Scanning</div>
        </div>
        <div class="pmc-aw-stream" id="pmcAuditStream"></div>
        <div class="pmc-aw-foot">
          <span>Findings refresh every 4s</span>
          <a href="#contact" class="pmc-aw-foot-cta">Run yours &rarr;</a>
        </div>
      </div>
    </div>
  </section>

  <!-- SERVICES -->
  <section class="pmc-section" id="services">
    <div class="pmc-container">
      <div class="pmc-services-head">
        <div class="editorial">
          <span class="eyebrow">Services</span>
          <h2 class="display h2" style="margin-top:14px">The whole funnel, run by one person.</h2>
        </div>
        <p class="body-sm" style="max-width:380px">
          Most consultants pick a lane and outsource the rest. Joe owns every layer, so the strategy, the execution, and the measurement actually talk to each other.
        </p>
      </div>

      <div class="pmc-services-grid">
        <div class="pmc-service-card">
          <h3 class="h4">Strategy &amp; growth audits</h3>
          <p class="body-sm">Full-funnel audit of your marketing stack, attribution, conversion, and messaging. Output is a 90-day plan, not a 40-page deck.</p>
          <div class="pmc-service-tags">
            <span class="pmc-service-tag">SEO</span>
            <span class="pmc-service-tag">Attribution</span>
            <span class="pmc-service-tag">CRO</span>
            <span class="pmc-service-tag">Messaging</span>
          </div>
        </div>
        <div class="pmc-service-card">
          <h3 class="h4">Paid media &amp; performance</h3>
          <p class="body-sm">Apple Search Ads, Meta, Google. Budgets sized to the goal, landing pages built to convert, attribution wired so you know what's working.</p>
          <div class="pmc-service-tags">
            <span class="pmc-service-tag">Apple Search Ads</span>
            <span class="pmc-service-tag">Meta</span>
            <span class="pmc-service-tag">Google</span>
          </div>
        </div>
        <div class="pmc-service-card">
          <h3 class="h4">Websites &amp; product surfaces</h3>
          <p class="body-sm">Webflow and React builds, design systems, embedded forms, scheduling, and the analytics layer underneath. Editorial-grade, performance-tuned.</p>
          <div class="pmc-service-tags">
            <span class="pmc-service-tag">Webflow</span>
            <span class="pmc-service-tag">React</span>
            <span class="pmc-service-tag">GA4 + GTM</span>
          </div>
        </div>
        <div class="pmc-service-card">
          <h3 class="h4">AI-assisted production</h3>
          <p class="body-sm">Custom Claude and GPT pipelines for content, video, briefs, and reporting. AI does the volume, I keep the judgment.</p>
          <div class="pmc-service-tags">
            <span class="pmc-service-tag">Content</span>
            <span class="pmc-service-tag">Video</span>
            <span class="pmc-service-tag">Reporting</span>
          </div>
        </div>
      </div>

      <a href="/services" class="pmc-link-arrow">
        See the full breakdown
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M5 12h14M12 5l7 7-7 7"/></svg>
      </a>
    </div>
  </section>

  <!-- AUDIT BANNER -->
  <section class="pmc-section-tight">
    <div class="pmc-container">
      <div class="pmc-audit-banner">
        <div class="pmc-audit-banner-left">
          <div class="pmc-audit-banner-icon">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
              <path d="M9 11l3 3L22 4"/>
              <path d="M21 12v7a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11"/>
            </svg>
          </div>
          <div>
            <div class="pmc-audit-banner-headline">Find out what's costing you leads.</div>
            <div class="pmc-audit-banner-sub">Complimentary audit - SEO, paid spend, attribution, conversion, messaging. No strings.</div>
          </div>
        </div>
        <a href="#contact" class="pmc-audit-banner-btn">
          Get my audit
          <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M5 12h14M12 5l7 7-7 7"/></svg>
        </a>
      </div>
    </div>
  </section>

  <!-- OPPORTUNITY -->
  <section class="pmc-section pmc-opportunity" id="opportunity">
    <div class="pmc-container">
      <div class="pmc-opportunity-head editorial">
        <span class="eyebrow">The opportunity</span>
        <h2 class="display h2" style="margin-top:14px">Most marketing setups break before they launch.</h2>
        <p class="body" style="margin-top:20px">
          A scan of 250 mid-market service business websites in 2025. Four common failure modes that show up in nearly every audit I run.
        </p>
      </div>
      <div class="pmc-gauges">
        <div class="pmc-gauge" data-target="70">
          <div class="pmc-gauge-ring">
            <svg viewBox="0 0 130 130" width="130" height="130" aria-hidden="true">
              <circle class="track" cx="65" cy="65" r="56"/>
              <circle class="progress" cx="65" cy="65" r="56" stroke-dasharray="351.86" stroke-dashoffset="351.86"/>
            </svg>
            <span class="pmc-gauge-num">0%</span>
          </div>
          <span class="pmc-gauge-label">No call to action above the fold</span>
        </div>
        <div class="pmc-gauge" data-target="53">
          <div class="pmc-gauge-ring">
            <svg viewBox="0 0 130 130" width="130" height="130" aria-hidden="true">
              <circle class="track" cx="65" cy="65" r="56"/>
              <circle class="progress" cx="65" cy="65" r="56" stroke-dasharray="351.86" stroke-dashoffset="351.86"/>
            </svg>
            <span class="pmc-gauge-num">0%</span>
          </div>
          <span class="pmc-gauge-label">CTAs misaligned with the funnel</span>
        </div>
        <div class="pmc-gauge" data-target="72">
          <div class="pmc-gauge-ring">
            <svg viewBox="0 0 130 130" width="130" height="130" aria-hidden="true">
              <circle class="track" cx="65" cy="65" r="56"/>
              <circle class="progress" cx="65" cy="65" r="56" stroke-dasharray="351.86" stroke-dashoffset="351.86"/>
            </svg>
            <span class="pmc-gauge-num">0%</span>
          </div>
          <span class="pmc-gauge-label">No CTAs on interior pages</span>
        </div>
        <div class="pmc-gauge" data-target="68">
          <div class="pmc-gauge-ring">
            <svg viewBox="0 0 130 130" width="130" height="130" aria-hidden="true">
              <circle class="track" cx="65" cy="65" r="56"/>
              <circle class="progress" cx="65" cy="65" r="56" stroke-dasharray="351.86" stroke-dashoffset="351.86"/>
            </svg>
            <span class="pmc-gauge-num">0%</span>
          </div>
          <span class="pmc-gauge-label">No analytics or attribution wired</span>
        </div>
      </div>
      <div class="pmc-opportunity-foot">
        <span>Sound like your setup?</span>
        <a href="#contact">Get an audit &rarr;</a>
      </div>
    </div>
  </section>

  <!-- PROCESS -->
  <section class="pmc-section">
    <div class="pmc-container">
      <div class="pmc-process-head editorial">
        <span class="eyebrow">How it works</span>
        <h2 class="display h2" style="margin-top:14px">Four phases. No black boxes.</h2>
      </div>
      <div class="pmc-process-grid">
        <div class="pmc-step">
          <span class="pmc-step-num">01</span>
          <div class="pmc-step-title">Discover</div>
          <p class="pmc-step-body">Two-week dig into your analytics, campaigns, content, and stack. Find what's broken, what's leaking, and where the leverage is.</p>
        </div>
        <div class="pmc-step">
          <span class="pmc-step-num">02</span>
          <div class="pmc-step-title">Strategize</div>
          <p class="pmc-step-body">A prioritized 90-day plan with real milestones. Not a 40-page deck. A working document we execute against.</p>
        </div>
        <div class="pmc-step">
          <span class="pmc-step-num">03</span>
          <div class="pmc-step-title">Execute</div>
          <p class="pmc-step-body">Hands-on implementation across SEO, paid, content, web, and tracking. AI does the volume, I keep the judgment. No junior handoffs.</p>
        </div>
        <div class="pmc-step">
          <span class="pmc-step-num">04</span>
          <div class="pmc-step-title">Iterate</div>
          <p class="pmc-step-body">Weekly check-ins, monthly readouts. Attribution, performance, and pipeline data drive the next cycle. The system gets sharper every quarter.</p>
        </div>
      </div>
    </div>
  </section>

  <!-- WHY PFENDER -->
  <section class="pmc-section" id="about">
    <div class="pmc-container">
      <div class="pmc-why-grid">
        <div class="pmc-why-aside">
          <span class="eyebrow">Why Pfender</span>
          <h2 class="display h2" style="margin-top:14px">Ten years in the trenches.</h2>
          <p class="body">Now I work for you.</p>
        </div>
        <div class="pmc-why-prose editorial">
          <p class="body">
            I spent a decade inside agency life - managing campaigns, building websites, and learning digital marketing alongside the people who actually moved the industry forward. SEO, paid media, analytics, web. I didn't specialize in one and outsource the rest. I learned all of it.
          </p>
          <p class="body">
            The bar for calling yourself a "digital marketer" has never been lower. Anyone with a Canva account and a ChatGPT login is an agency owner now. Very few of us have spent ten years deep in the work, across every channel, every platform, with a real passion for helping founders grow.
          </p>
          <p class="pull">
            That's what you're getting. Not a team of juniors. Not a salesperson who disappears after the contract is signed. One senior operator who does the work, owns the results, and actually gives a damn.
          </p>
          <p class="body">
            I'm based in Manayunk, Philadelphia. I work with founders nationwide. AI sits inside every part of my workflow - briefs, content, video, reporting - but every recommendation, every pitch, every line of strategy is mine.
          </p>
          <a href="/about" class="pmc-link-arrow" style="margin-top:24px">
            More about how I work
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M5 12h14M12 5l7 7-7 7"/></svg>
          </a>
        </div>
      </div>
    </div>
  </section>

  <!-- WORK -->
  <section class="pmc-section pmc-work" id="work">
    <div class="pmc-container">
      <div class="pmc-work-head">
        <div class="editorial">
          <span class="eyebrow">Recent work</span>
          <h2 class="display h2" style="margin-top:14px">Founder-led businesses, real numbers.</h2>
        </div>
        <a href="/case-studies" class="pmc-link-arrow">
          All case studies
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M5 12h14M12 5l7 7-7 7"/></svg>
        </a>
      </div>

      <div class="pmc-work-grid">
        <!-- HORSEGRID -->
        <div class="pmc-work-card">
          <div class="pmc-work-meta">
            <span>HorseGrid Designer</span>
            <span class="pill">Live retainer</span>
          </div>
          <h3 class="pmc-work-title">An equestrian iOS app, patent pending, launching version 1.1.</h3>
          <p class="pmc-work-blurb">
            Full marketing stack for a founder-led iOS company. Brand identity, website, App Store positioning, attribution, paid acquisition strategy, and content engine - all built and run by Joe.
          </p>
          <div class="pmc-metrics">
            <div>
              <div class="pmc-metric-label">Engagement</div>
              <div class="pmc-metric-num">$2k</div>
              <div class="pmc-metric-context">Monthly retainer</div>
            </div>
            <div>
              <div class="pmc-metric-label">Patent</div>
              <div class="pmc-metric-num">Pending</div>
              <div class="pmc-metric-context">USPTO filed</div>
            </div>
            <div>
              <div class="pmc-metric-label">App version</div>
              <div class="pmc-metric-num">1.1</div>
              <div class="pmc-metric-context">Q2 2026 launch</div>
            </div>
          </div>
          <div class="pmc-work-quote">
            <p class="pmc-work-quote-text">"Joe built the entire marketing system around the app. Every dollar I spend has a number attached now."</p>
            <p class="pmc-work-quote-author">- Tessa Vosika, Founder of HorseGrid Designer</p>
          </div>
          <a href="/case-studies/horsegrid" class="pmc-work-link">
            Read the case study
            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M5 12h14M12 5l7 7-7 7"/></svg>
          </a>
        </div>

        <!-- VITALITY -->
        <div class="pmc-work-card coming-soon">
          <div class="pmc-work-meta">
            <span>Vitality Wellness Collective</span>
            <span class="pill queued">Launching May 8</span>
          </div>
          <h3 class="pmc-work-title">A wellness coaching practice, brand-new website, end-to-end ops.</h3>
          <p class="pmc-work-blurb">
            Brand, website, Tree CRM workspace, and a 90-day go-to-market roadmap for Sean Calhoun's coaching practice. Designed, built, and shipped inside three weeks.
          </p>
          <div class="pmc-metrics">
            <div>
              <div class="pmc-metric-label">Scope</div>
              <div class="pmc-metric-num">Full</div>
              <div class="pmc-metric-context">Brand + site + CRM</div>
            </div>
            <div>
              <div class="pmc-metric-label">Timeline</div>
              <div class="pmc-metric-num">21d</div>
              <div class="pmc-metric-context">Discovery to live</div>
            </div>
            <div>
              <div class="pmc-metric-label">Roadmap</div>
              <div class="pmc-metric-num">90d</div>
              <div class="pmc-metric-context">Launch to scale</div>
            </div>
          </div>
          <div class="pmc-work-quote">
            <p class="pmc-work-quote-text">"Case study coming after Sean's site goes live."</p>
            <p class="pmc-work-quote-author">- May 8, 2026</p>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- CONTACT -->
  <section class="pmc-section" id="contact">
    <div class="pmc-container">
      <div class="pmc-contact-head editorial">
        <span class="eyebrow">Let's talk</span>
        <h2 class="display h2" style="margin-top:14px">Ready to get unstuck?</h2>
        <p class="body" style="margin-top:14px">
          Start with an audit. No pitch deck, no pressure. If we're a fit, we'll scope it. If we're not, you'll still walk away with a sharper view of where you're losing leads.
        </p>
      </div>
      <div class="pmc-contact-grid">
        <div>
          <div class="pmc-contact-trust">
            <div class="pmc-trust-row">
              <div class="pmc-trust-check">
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M5 13l4 4L19 7"/></svg>
              </div>
              <span class="pmc-trust-text">No long-term contracts required</span>
            </div>
            <div class="pmc-trust-row">
              <div class="pmc-trust-check">
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M5 13l4 4L19 7"/></svg>
              </div>
              <span class="pmc-trust-text">Working directly with the operator, not an account team</span>
            </div>
            <div class="pmc-trust-row">
              <div class="pmc-trust-check">
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M5 13l4 4L19 7"/></svg>
              </div>
              <span class="pmc-trust-text">Audit returned within 5 business days</span>
            </div>
            <div class="pmc-trust-row">
              <div class="pmc-trust-check">
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M5 13l4 4L19 7"/></svg>
              </div>
              <span class="pmc-trust-text">30-minute intro call, no pitch deck</span>
            </div>
          </div>
        </div>
        <div class="pmc-contact-card">
          <div class="pmc-contact-card-row">
            <span class="label">Email</span>
            <span class="value"><a href="mailto:joe@pfendermarketing.com">joe@pfendermarketing.com</a></span>
          </div>
          <div class="pmc-contact-card-row">
            <span class="label">Phone</span>
            <span class="value"><a href="tel:+17173716816">(717) 371-6816</a></span>
          </div>
          <div class="pmc-contact-card-row">
            <span class="label">Based</span>
            <span class="value">Manayunk, Philadelphia</span>
          </div>
          <div class="pmc-contact-card-actions">
            <a href="/start" class="pmc-btn pmc-btn-primary">Apply for an audit
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M5 12h14M12 5l7 7-7 7"/></svg>
            </a>
            <a href="/meeting" class="pmc-btn pmc-btn-secondary">Book an intro call
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M9 5l7 7-7 7"/></svg>
            </a>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- FOOTER -->
  <footer class="pmc-footer">
    <div class="pmc-container">
      <div class="pmc-footer-grid">
        <div class="pmc-footer-brand">
          <a href="#" class="pmc-nav-logo">
            <span class="pmc-nav-logo-mark">
              <svg viewBox="0 0 24 24" fill="none" aria-hidden="true">
                <path d="M5 18 L9 14 L7 12 L11 8 L13 10 L17 6" stroke="#457340" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                <rect x="6" y="16" width="2" height="3" fill="#D7AF33"/>
                <rect x="10" y="14" width="2" height="5" fill="#D7AF33"/>
                <rect x="14" y="10" width="2" height="9" fill="#D7AF33"/>
              </svg>
            </span>
            Pfender Marketing Co.
          </a>
          <p class="pmc-footer-blurb">Senior, AI-augmented marketing for founder-led businesses. Built in Philadelphia. Working nationwide.</p>
        </div>
        <div class="pmc-footer-col">
          <div class="pmc-footer-col-title">Work with me</div>
          <ul>
            <li><a href="/services">Services</a></li>
            <li><a href="/case-studies">Case studies</a></li>
            <li><a href="/start">Start a project</a></li>
            <li><a href="/meeting">Book a call</a></li>
          </ul>
        </div>
        <div class="pmc-footer-col">
          <div class="pmc-footer-col-title">Products</div>
          <ul>
            <li><a href="/tree-crm">Tree CRM</a></li>
            <li><a href="/about">About</a></li>
            <li><a href="/contact">Contact</a></li>
          </ul>
        </div>
        <div class="pmc-footer-col">
          <div class="pmc-footer-col-title">Direct</div>
          <ul>
            <li><a href="mailto:joe@pfendermarketing.com">joe@pfendermarketing.com</a></li>
            <li><a href="tel:+17173716816">(717) 371-6816</a></li>
            <li><a href="https://www.linkedin.com/in/joepfender" target="_blank" rel="noopener">LinkedIn</a></li>
          </ul>
        </div>
      </div>
      <div class="pmc-footer-fine">
        <span>&copy; 2026 Pfender Marketing Co. All rights reserved.</span>
        <div class="pmc-footer-legal-links">
          <a href="/privacy">Privacy</a>
          <a href="/terms">Terms</a>
        </div>
      </div>
    </div>
  </footer>

</div>`;
const cm = document.activeElement.closest('.cm-editor')?.querySelector('.cm-content')
       || document.querySelector('.cm-editor:not([data-ready]) .cm-content');
if (!cm) return { ok: false, err: 'No active CM6 editor for embed' };
cm.focus();
const dt = new DataTransfer();
dt.setData('text/plain', BODY_HTML);
cm.dispatchEvent(new ClipboardEvent('paste', { clipboardData: dt, bubbles: true }));
await new Promise(r => setTimeout(r, 250));
return {
  ok: (cm.textContent || '').length >= BODY_HTML.length * 0.95,
  insertedLen: (cm.textContent || '').length,
  expectedLen: BODY_HTML.length
};
```

Expected return: `insertedLen: ~25047`.

5. Click **Save & Close** on the Embed modal.

### 7. Save Designer state, do NOT publish

1. Webflow auto-saves Designer changes. You may also see a "Save" button in the top bar - if it's there, click it.
2. **Stop here.** Do not click Publish. Do not click Stage. Do not run any deployment.
3. Report the three paste results to Joe with `insertedLen` vs `expectedLen` for each.

### 8. Visual verification (optional, before reporting)

If everything looks clean, you can preview the staging URL:

1. In the Designer top bar, click the eye/preview icon to open `pmc-staging.webflow.io/home-staging` in a new tab.
2. Compare visually against `outputs/pmc-homepage-mockup.html` if accessible. Or just report what the page looks like.
3. Do NOT publish to the production custom domain.

## Failure paths

| Symptom | Action |
| --- | --- |
| Heartbeat fails / MCP timeout | Ask Joe to refresh the Webflow Designer tab and bring it to the foreground. Retry. |
| `pasteIntoCMEditor` returns `ok: false` | Stop. Report the diff (`insertedLen` vs `expectedLen`). Do NOT retry without changing approach. |
| Designer modal doesn't open Custom Code section | The CM6 editors only render once the Custom Code panel is expanded. Click to expand. |
| MCP Bridge App modal blocking the canvas | Close it before any other action. |
| Embed editor doesn't accept the paste | The Embed modal sometimes needs a second click on the editor area to focus it. Click into the editor area first, then re-run the paste JS. |

## What "done" looks like

When you report back to Joe, include:
1. Three `pasteIntoCMEditor` results with `ok: true` and `insertedLen` close to expected
2. Screenshot of the Designer canvas showing the new homepage rendering (or a note if visual verification is deferred)
3. Confirmation that **no publish** was attempted

That's it.

---

## PAYLOADS

Joe: replace the three `__..._HERE__` placeholders above with the contents of these files in your local repo. Open each file in VS Code and copy the entire contents.

- `__HEAD_CSS_HERE__` ← contents of `pmc-webflow-project/pages/home-draft/head.html`
- `__BODY_JS_HERE__` ← contents of `pmc-webflow-project/pages/home-draft/body.html`
- `__PAGE_BODY_HERE__` ← contents of `pmc-webflow-project/pages/home-draft/embeds/page-body.html`

Backticks inside the file contents will break the template literal wrapper. If you hit that, switch the wrapper to a different delimiter or pass the content via a different mechanism (e.g., escape backticks with `\`` or assign via `JSON.parse(...)` of a base64-encoded string).

Expected file sizes:
- head.html: ~25,092 bytes
- body.html: ~9,161 bytes
- page-body.html: ~25,047 bytes
