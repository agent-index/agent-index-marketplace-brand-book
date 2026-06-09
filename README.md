# Brand Book Collection

The org's single source of truth for how produced artifacts look, sound, and feel. A design-oriented member defines design tokens, voice, display elements, and artifact templates; every collection that supports the brand book picks them up automatically. Artifacts stay org-branded — counterparty content (like a client's logo) appears only in template-designated slots, supplied by the consuming collection at compose time.

## What's Included

### Foundations & Content
- **edit-brand** — design tokens (palette, typography, spacing, iconography) + voice and tone
- **create-element / edit-element** — display elements (14 starters: cover-page, header, footer, table, list, callout, title-block, metadata-block, chart-style, kpi-card, toc, section-divider, code-block, quote — plus custom), drafted privately with per-format renderings (markdown, docx, pptx, html, pdf)
- **create-brand-template / edit-brand-template** — artifact templates: section composition + counterparty slots
- **manage-assets** — named brand assets (logos, icons, patterns)

### Lifecycle
- **share-draft** — share a draft with reviewers (read) or collaborators (read + write); owner-applied verified grants
- **publish-brand-item** — confirmation-gated, attributed, versioned publication into the org brand book
- **preview-brand** — render a sample artifact with the current brand; the design iteration loop

### Provider Operations (consumed by other collections)
- **get-brand-guidelines, get-element, get-template, get-asset** — read-only ops behind the `brand-book` capability (registered at install; see `agent-index-core/capability-types/brand-book.json`)

### Onboarding
- **brand-book-tutorial**

## Data Location & Access

Published brand: `/shared/brand-book/` (open commons — every member reads; publishing is confirmation-gated, attributed, and activity-logged). Drafts: the author's own private space, invisible until shared (pointers at `/shared/brand-book-index/`). Personal elements (when `brand_usage` is `optional`): member-local. The collection ships **no fonts and no logos** — org design teams add their own.

## Enforcement

One org-wide switch, `brand_usage` (`required` | `optional`), set at install and carried in the provider registry. Undefined elements always fall back to native defaults — nothing ever blocks artifact production.

## Requirements

agent-index-core ≥ 3.10.0 and agent-index-marketplace ≥ 2.10.0 (capability-provider runtime V1).

## Version

1.0.1

## Version History

See CHANGELOG.md.
