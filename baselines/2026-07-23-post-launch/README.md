# PMC Webflow Post-Launch Baseline

This directory preserves the verified Website state following the July 23, 2026 production launch.

## Authority

The live production responses are the authority for what visitors received at capture time. The Webflow API extraction preserves the current project structure, settings, styles, code, CMS, assets, and stable IDs. Because the Webflow project was updated after its last publication, Designer data must not be assumed to be published unless it is also present in the production snapshot.

## Captured state

- Internal Webflow workspace/site identifiers, staging preview details, and publication metadata are withheld from the public repository.
- Extraction completed: `2026-07-23T18:13:11Z`
- Historical repository base: `d41e5ad92115fcd5d296908e7e399c68abc1aebf`
- Baseline branch: `agent/webflow-post-launch-baseline-2026-07-23`

## Directory map

- `raw/production/`: production HTML, the exact shared Webflow CSS asset, and the production sitemap
- `raw/webflow/`: Webflow project metadata, page trees, custom code, scripts, schema, styles, variables, CMS, assets, fonts, components, and sitemap settings
- `manifest.json`: provenance, counts, authority rules, and limitations
- `production-observations.json`: observed status and redirect behavior
- `comparison-to-may-repository.md`: reconciliation against the former repository state
- `approved-website-design-system.md`: approved design and responsive rules
- `SHA256SUMS`: integrity hashes for every raw artifact
- Retired Make webhook endpoint values are intentionally replaced with a nonfunctional redaction marker. This affects only the preserved capture, not Webflow.
- One illustrative customer-record code example is replaced with a repository-redaction marker in the production Blog capture and CMS export. This removes contact, consent, value, and service data from the public repository without changing the live Website or Webflow.
- The raw Webflow asset inventory is represented by a metadata-only stub because original filenames and metadata may expose client or customer naming. Production HTML and CSS continue to preserve the public asset references observed at capture time.
- The raw Webflow CMS export is represented by a metadata-only stub because it includes five unpublished drafts and private source-derived business material. The 34 production Blog pages remain captured individually from the public Website.
- The raw Webflow site metadata response is represented by a checksum stub so internal workspace/site identifiers, staging preview details, and publication metadata are not exposed publicly.

## Reading rules

1. Use `raw/production/` to answer what was live at capture time.
2. Use `raw/webflow/` to restore or inspect Webflow structure and exact project settings.
3. Treat files with `-retry` as successful replacements for same-page reads that initially hit Webflow rate limits.
4. Do not reactivate preserved hidden Audit CTA source from this baseline. The Audit CTA has a separate active workstream.
5. Do not copy the May page files into Webflow without reconciling them against this baseline.

## Verification boundary

The Website launch was already verified on production for responsive layout, form rendering, booking behavior, navigation, schema, console, and overflow. The later Tree CRM form release was independently deployed at commit `9d02b8a4162b0328f0e053fda491598ecaf89b39`.

No Webflow, production, Tree CRM, CMS, analytics, or Audit CTA change was made while creating this baseline.
