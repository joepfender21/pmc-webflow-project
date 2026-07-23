# Comparison to the May Repository

The May repository is useful historical evidence, but it is not an accurate representation of the July 23 production Website.

## Scope difference

| Area | May repository | July 23 baseline |
| --- | --- | --- |
| Tracked repository files before baseline | 35 | 56 Webflow raw artifacts plus production runtime snapshots and baseline documentation |
| Page coverage | Home draft, Services, Start, Meeting, Case Studies | 17 Webflow pages, including Blog, legal, utility, local landing drafts, and a CMS template |
| CMS | Not captured | Blog schema plus 37 current CMS items |
| Production routes | Hand-maintained page folders | Exact 40-URL production sitemap plus 34 live Blog response snapshots |
| Styles | Page CSS fragments | Exact production shared CSS and all 1,523 Webflow styles with properties |
| Stable IDs | Mostly absent | Site, page, element, component, collection, asset, font, script, style, and variable IDs |
| Components | Not modeled | Global navigation component and instance structure |
| Runtime authority | Claimed repository copy-paste workflow | Production HTML/CSS for live truth, Webflow for project truth, GitHub for review and history |

## Material contradictions

1. The old `CLaude.md` says the repository is the Website source of truth. That is no longer accurate. Webflow owns runtime structure and publication; this repository now preserves exact extractions and reviewed change history.
2. The old typography note specifies a 700-weight 60px H1. The approved Home hero is Clash Display 500 with `clamp(42px, 6.4vw, 92px)`, a 780px maximum width, and a 34px phone override.
3. The old page-status section calls Home a draft and Case Studies staging. Home is now production. Case Studies redirects to Home.
4. The old form section describes Make.com form posting. The live Home and Services forms are Tree CRM embeds.
5. The old layout and code files do not contain the current global navigation component, Blog CMS, current native schema, current redirects, current page metadata, or the post-launch script cleanup.
6. The old Home content and hero do not include the approved statement, three-line width, medium weight, or final-word gold emphasis.

## Preserved historical value

The old files remain useful for:

- reconstructing the development sequence
- identifying previously removed code
- reviewing original page-level CSS and JavaScript
- understanding why Webflow embed safety rules were adopted
- comparing future changes against the pre-launch implementation

They should not be copied to production or treated as current without reconciling them against this baseline.

## Result

No historical page code was overwritten. The baseline is additive, and the safe Website OS index receives a status warning so future agents do not mistake May instructions for July runtime truth.
