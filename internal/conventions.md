# Brand-Book Internal Conventions (referenced by all editor tasks)

**Draft space (owned-content):** `id:{member_folder_id}/brand-book/{kind}/{slug}/` where kind ∈ elements|templates|tokens-voice. Draft payload file: `element.json` / `template.json` / token+voice files mirroring the published layout. Drafts are invisible until shared (no pointer).

**Draft sharing:** owner-applied grants via permission-change-helper ONLY (reader = "share with X", writer = "make X a collaborator"). HARD GATE: pointer/confirmation only after outcome `"applied"` or independent `aifs_get_permissions`. Pointer on first share: `/shared/brand-book-index/{owner_hash}-{kind}-{slug}.json` `{type:"brand-draft", kind, name, slug, owner, owner_hash, status:"active", scope:{readers,collaborators}, location:{folder_id}, created, last_updated}` — overwrite-only; unshare = grants revoked + scope `"revoked"`.

**Published commons:** `/shared/brand-book/` per lib/schemas. Publishing (publish-brand-item) is the ONLY path from draft to commons. Slug uniqueness enforced against the commons listing at publish time. All commons writes attributed (`author_hash`, `author_name`) + appended to `/shared/brand-book/activity-log.jsonl` (revision-aware: `aifs_stat` → `if_revision`, retry on `REVISION_CONFLICT`, cap 5).

**Tool families:** drafts and commons are REMOTE (`aifs_*`, `id:` anchors for all draft/cross-space paths). Personal elements are LOCAL: `members/{member_hash}/brand-book/personal/{elements|templates}/{slug}.json`, native Read/Write. Precedence (enforced by CONSUMERS, not this collection): brand_usage=required → org wins, personal only for org-undefined element types; optional → personal overrides org.

**Starter element slugs (14):** cover-page, header, footer, table, list, callout, title-block, metadata-block, chart-style, kpi-card, toc, section-divider, code-block, quote. These are reserved names with documented intent — the collection ships NO content for them.

**aifs_delete is non-recursive** — any hard delete removes contents first. Prefer archive/revoke conventions on shared surfaces.
