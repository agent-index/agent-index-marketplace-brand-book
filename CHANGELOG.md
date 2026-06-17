# Brand Book Collection — Changelog

## [1.1.0] — 2026-06-17 — interactive templates & action markers

### Added

- **Action markers (elements).** `element-schema.json` gains an optional `markers[]` field: neutral, behavior-free insertion points an element exposes (e.g. `card.actions`, `header.actions`). Generic elements stay reusable; they never name what fills a marker. `get-element` now passes `markers` through verbatim.
- **Interactive/domain templates.** `template-schema.json` gains optional, back-compatible fields: `section.repeat:"per-item"` + `data_binding` (repeat an element over consumer-supplied items), `marker_fills[]` (a domain template places concrete buttons into an element's markers, each carrying an abstract action handle), `action_contract[]` (the handles the template emits, which the consuming collection maps to its own flows), and `surfaces` (`cowork_interactive` default + `export_formats`). `get-template` passes these through verbatim.
- **Convention documented.** `internal/conventions.md` > "Interactive templates & action markers" defines the standard split — generic markered elements + a domain template that fills them + a consumer that maps handles to flows — so future directory/list templates inherit one solution instead of inventing their own.

### Notes

- Fully back-compatible: plain document templates omit all v1.1 fields; capability version stays 1.0.0 (provider op signatures unchanged — additive fields only). Ships no content (the brand book ships empty — orgs author their own elements/templates); this release adds only the schema, passthrough, and convention. The first consuming collection is client-intelligence (`list-clients` / `view-client`), which looks up a brand template by its artifact type.


## [1.0.1] — 2026-06-08 — alias collision fix (BB-F1)

### Changed

- Renamed tasks `create-template` → `create-brand-template` and `edit-template` → `edit-brand-template` (files, frontmatter, manifests, setup templates, collection.json api[], README). These collided with client-intelligence's identically-named tasks; install-collection's alias resolver handled it per-install, but the rename makes the names deterministic across every org (and matches the resolved aliases the live install already produced). bug 20260608 BB-F1.

## [1.0.0] — 2026-06-07

### Added

- Initial release: 14 API members across foundations (edit-brand), elements (create/edit-element), templates (create/edit-template, with counterparty slots), assets (manage-assets), lifecycle (share-draft, publish-brand-item, preview-brand), the four `brand-book` capability provider operations (get-brand-guidelines, get-element, get-template, get-asset), and brand-book-tutorial.
- First registered capability provider in agent-index: exercises the provider runtime V1 (core 3.10.0 / marketplace 2.10.0) end to end. First consumer: client-intelligence 2.1.0.
- Access model: owned-content drafts + open-commons published space + promotion-as-relocation; `collaborative-acls.json` provisions all@ writer on `/shared/brand-book/` and `/shared/brand-book-index/`.

### Requires Admin Attention

- Requires core 3.10.0 + marketplace 2.10.0 first (capability runtime). Step 5.7 will offer provider registration at install — accept it, or consumers will not discover the brand book.
