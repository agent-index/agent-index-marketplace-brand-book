# Brand Book Collection — Changelog

## [1.2.1] — <RELEASE_DATE> — install paths and index folder

### Fixed (4.0.1 pass — OneDrive and Google Drive live tests)
- **Setup creates `/shared/brand-book-index/` (with a placeholder README) before its grants are provisioned (finding 55).**
- `*-setup.md` files name the collection-qualified install path, with the correct `task` type for the nine tasks that were listed under `skills/` (finding 14).
- **share-draft 1.1.1 reads the members registry by path on OneDrive** (finding 63).

## [1.2.0] — 2026-10-02 — Draft sharing through the core permission-changes procedure

**MINOR — requires agent-index-core 3.32.0.** Part of retiring the permission-change-helper (design record: `/shared/projects/core-improvements/artifacts/retire-permission-helper-*.md`).

### Changed
- **`share-draft` 1.1.0** — grants and removals go through core `internal/permission-changes.md`. The owner sees the exact table and confirms it (removals one at a time), and each change is verified. Partial results are now handled: continue with what was applied and report the rest. The pointer scope is rebuilt from a fresh `aifs_get_permissions` read after every share and unshare: explicit grants only, owner excluded, writer → collaborators, reader/commenter → readers; `"revoked"` only when nobody other than the owner remains. Each change is spelled out in full (with a short `reason`). "Who can already see it" comes from the permissions read, not the pointer. The members registry is read by id anchor (`resource_ids.members_registry`).
- **Recipients outside the org are allowed** (by email; flagged **outside your org**). Previously such people were skipped.
- **`internal/conventions.md`** — draft-sharing convention updated to match, plus how a shared draft is used: reviewers open it from their storage app's "Shared with me"; collaborators edit via `edit-element` / `edit-brand-template`; pointers with `status: "published"` are ignored.
- **Requirements** — README now states agent-index-core ≥ 3.32.0 (was 3.10.0).
- `share-draft`, `edit-element`, `edit-brand-template` manifests: `writes_to` now matches the task frontmatter (was `null`).

### Fixed
- **Shared drafts couldn't be opened across drives (C.1.3 `crossdriveread` gap).** Draft-share pointers recorded only `folder_id`, so on OneDrive a recipient's bare `id:{folder_id}` resolved against their own drive and returned PATH_NOT_FOUND. Now `share-draft` captures the draft's home `drive_id` and writes `location.item_drive_id` (backfilling older pointers on the next share). `share-draft` sets `item_drive_id` on every pointer overwrite (share, unshare, nothing-to-change). `edit-element` 1.0.1 and `edit-brand-template` 1.0.1 open **and write** a collaborated draft via `id:{item_drive_id}:{folder_id}/…` (bare `id:{folder_id}/…` for old pointers), never the collaborator's own draft path; on a bare-anchor PATH_NOT_FOUND they ask the owner to re-run share-draft, and they skip `status: "published"` pointers. Same fix projects, strategy and library got in C.1.3.
- `collection.json` natural-language triggers for `create-brand-template` / `edit-brand-template` pointed at the pre-1.0.1 names `create-template` / `edit-template`; they now name the real capabilities.
- Every `api/*-manifest.json` `collection_version` and the README version stanza were still `1.0.1` under collection 1.1.0. They now track the collection version.

### License
- Proprietary, as before: `LICENSE` added and the `collection.json` `license` field set to `proprietary` (it said `open`).

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
