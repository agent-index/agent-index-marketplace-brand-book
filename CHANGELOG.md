# Brand Book Collection — Changelog

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
