---
name: create-element
type: task
version: 1.0.0
collection: brand-book
description: Draft a new display element (or a starter element definition) in the author's private space, with per-format renderings.
stateful: false
produces_artifacts: false
produces_shared_artifacts: false
dependencies:
  skills: []
  tasks: []
external_dependencies: []
reads_from: "/shared/brand-book/"
writes_to: "member draft space (id: anchor)"
---
## About This Task

Starts a new element DRAFT in the member's own space (invisible until shared or published). Conventions: `internal/conventions.md`.

### Inputs
Element name (or one of the 14 starter slugs), semantic intent, renderings per format the author wants to define now.

### Outputs
- `id:{member_folder_id}/brand-book/elements/{slug}/element.json` (schema: `lib/schemas/element-schema.json`)

## Workflow

### Step 1: Identify and Scope
Read local `member-index.json` (`member_hash`, `member_folder_id`). Ask which element (offer the starter list for undefined ones — read `/shared/brand-book/elements/` listing via `aifs_list` to show what's already published). Slugify; if a DRAFT with this slug already exists in the member's space, offer edit-element instead.

### Step 2: Define
Conversationally collect: semantic intent (what is this element FOR), then renderings for the formats the author cares about now (markdown/docx/pptx/html/pdf — any subset; absent formats degrade gracefully at consume time). Encourage `{token:...}` references over literal values where tokens exist (read `/shared/brand-book/tokens/` to offer them). Show the assembled element.json for confirmation.

### Step 3: Write
On confirmation: `aifs_write` the draft via `id:` anchor. Confirm: "Draft saved (private). Share it for review with 'share my brand draft', or publish with 'publish brand element'."

**On write failure:** surface the path and reason; nothing else was changed.

## Directives

### Behavior
Design-partner tone; the author may be a designer, not a technologist. Translate their intent into rendering instructions; read back in plain language.

### Constraints
Never write to `/shared/brand-book/` (publishing is publish-brand-item's job). Never apply permissions. Slug-only names; reject path separators.

### Edge Cases
Published element with same slug exists → allowed (draft proposes a new version; publish task handles the version bump). No tokens defined yet → fine; literal values with a note that edit-brand can centralize them later.
