# Brand-Book Internal Conventions (referenced by all editor tasks)

**Draft space (owned-content):** `id:{member_folder_id}/brand-book/{kind}/{slug}/` where kind ∈ elements|templates|tokens-voice. Draft payload file: `element.json` / `template.json` / token+voice files mirroring the published layout. Drafts are invisible until shared (no pointer).

**Draft sharing:** owner-applied grants via permission-change-helper ONLY (reader = "share with X", writer = "make X a collaborator"). HARD GATE: pointer/confirmation only after outcome `"applied"` or independent `aifs_get_permissions`. Pointer on first share: `/shared/brand-book-index/{owner_hash}-{kind}-{slug}.json` `{type:"brand-draft", kind, name, slug, owner, owner_hash, status:"active", scope:{readers,collaborators}, location:{folder_id}, created, last_updated}` — overwrite-only; unshare = grants revoked + scope `"revoked"`.

**Published commons:** `/shared/brand-book/` per lib/schemas. Publishing (publish-brand-item) is the ONLY path from draft to commons. Slug uniqueness enforced against the commons listing at publish time. All commons writes attributed (`author_hash`, `author_name`) + appended to `/shared/brand-book/activity-log.jsonl` (revision-aware: `aifs_stat` → `if_revision`, retry on `REVISION_CONFLICT`, cap 5).

**Tool families:** drafts and commons are REMOTE (`aifs_*`, `id:` anchors for all draft/cross-space paths). Personal elements are LOCAL: `members/{member_hash}/brand-book/personal/{elements|templates}/{slug}.json`, native Read/Write. Precedence (enforced by CONSUMERS, not this collection): brand_usage=required → org wins, personal only for org-undefined element types; optional → personal overrides org.

**Starter element slugs (14):** cover-page, header, footer, table, list, callout, title-block, metadata-block, chart-style, kpi-card, toc, section-divider, code-block, quote. These are reserved names with documented intent — the collection ships NO content for them.

**aifs_delete is non-recursive** — any hard delete removes contents first. Prefer archive/revoke conventions on shared surfaces.

---

## Interactive templates & action markers (convention, v1.1+)

The standard pattern for any template that renders an **interactive directory/list** (clients, projects, accounts, people) with per-item or page-level actions. Follow it so every collection gets the same solution instead of inventing its own.

**The split (this is the whole convention):**

- **Elements stay generic.** A reusable element (for example a card element, or a list/section header) may expose neutral, behavior-free **`markers`** — named insertion points like `actions`. The element NEVER names what goes in a marker. This keeps elements reusable across collections.
- **The template is domain-specific and fills the markers.** A domain template (for example a directory or profile template) uses **`marker_fills`** to place concrete buttons into an element's markers, each carrying a label and an **abstract action handle** (`add`, `edit`, `archive`, …) — not a flow name. The template declaring buttons is allowed and expected; that is where use-case knowledge lives.
- **The consumer maps handles to flows.** The template's **`action_contract`** lists each handle and says "consumer must map this to its create / edit / archive flow." The consuming collection (via `/internal/resolve-brand.md`) binds each handle to one of its own tasks. The brand book never references another collection's tasks.

**Supporting schema fields (all optional, back-compatible — see `lib/schemas/template-schema.json`):**

- `section.repeat: "per-item"` + `data_binding` — render a repeating element once per consumer-supplied item; `item_field_map.item_key` identifies the item for item-scoped actions.
- `marker_fills[]` — `{target: "<element>.<marker>", buttons: [{label, element, variant, icon?, action, scope, confirm?}]}`. `scope` is `collection` (page-level) or `item` (per row/card).
- `action_contract[]` — `{handle, scope, intent, consumer_must_map_to, confirm?}`.
- `surfaces` — `default` surface, a `cowork_interactive` block, and `export_formats` for static renders. Static exports omit action buttons unless an interactive export is requested.
- Elements expose insertion points via `markers[]` in `element-schema.json` (`{name, placement, accepts, description}`).

**Authoring flow:** generic markered elements via `create-element`; the domain template via `create-brand-template` (fill `marker_fills` + `action_contract` + `surfaces`); publish both with `publish-brand-item`. A consuming collection looks up a brand template by its own artifact type and renders it when present — so an org gets branded interactive views by publishing a template under that artifact-type name; no code change in the consumer is needed.
