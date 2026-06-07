---
name: publish-brand-item
type: task
version: 1.0.0
collection: brand-book
description: Publish a draft element or template into the org brand book (promotion-as-relocation with confirmation, attribution, and activity logging).
stateful: false
produces_artifacts: false
produces_shared_artifacts: true
dependencies:
  skills: []
  tasks: []
external_dependencies: []
reads_from: "/shared/brand-book/"
writes_to: "/shared/brand-book/"
---
## About This Task

The ONLY path from draft to published. Promotion-as-relocation per projects-4.0 mechanics: verified commons write FIRST, then pointer flip, then draft stub. Conventions: `internal/conventions.md`.

### Inputs
Draft slug (element or template).

### Outputs
- `/shared/brand-book/{elements|templates}/{slug}.json` (versioned, attributed, provenance block)
- Activity-log event; updated/created index pointer; draft stubbed.

## Workflow

### Step 1: Locate + Validate
Read the draft (id: anchor). Validate against the schema (`lib/schemas/`). For templates: check element references against published elements + starter slugs — unpublished refs are a WARNING in the confirmation, not a block. Slug check against `/shared/brand-book/{kind}/` listing: if the slug is NEW → version 1; if it EXISTS → this is a revision: read the published file, next version = published.version + 1, and show a summary diff in the confirmation.

### Step 2: Confirm (explicit gate)
> Publish '{name}' to the org brand book?
> {new | revision: vN → vN+1, summary of changes}
> {warnings if any}
> Every member can read it; consuming collections pick it up from their next composition. This action is attributed to you.

### Step 3: Write — ordered
1. Compose the published file: draft content + `version` + `author_hash`/`author_name` + `last_updated` + `provenance {published_from: draft slug, published_date}`.
2. `aifs_write` to the commons. **Verify** by reading back (size/parse). Only then:
3. Append attributed activity-log event (revision-aware, `if_revision`).
4. If a draft-share pointer exists: overwrite it with `status: "published"` and `location: {path: "/shared/brand-book/{kind}/{slug}.json"}`.
5. Stub the draft: overwrite the draft payload with `{"published": "vN", "see": "/shared/brand-book/{kind}/{slug}.json"}` (the draft folder and any reviewer grants remain harmless).

**On failure at any point:** STOP, report exactly which writes landed. Never flip the pointer before the verified commons write (step 2-before-3 ordering is the contract).

## Directives

### Behavior
Open-commons governance is the confirmation + attribution + log — make the org-wide effect unmistakable in the prompt.

### Constraints
Slug uniqueness is enforced HERE (no same-name siblings — duplicate-name bug discipline). Never publish foundations (edit-brand's domain). Never delete the draft (stub only).

### Edge Cases
Concurrent publish of the same slug (rare): the read-back verify catches a different author's content → surface both, do not overwrite without explicit member choice. Activity-log conflict → retry per convention; if the log write ultimately fails, the publish STANDS (content + attribution in the file) — report the log gap.
