---
name: edit-brand
type: task
version: 1.0.0
collection: brand-book
description: Edit the org's design tokens, voice definition, and tone modifiers (the published brand foundations).
stateful: false
produces_artifacts: false
produces_shared_artifacts: true
dependencies:
  skills: []
  tasks: []
external_dependencies: []
reads_from: "/shared/brand-book/"
writes_to: "/shared/brand-book/tokens/, /shared/brand-book/voice/"
---
## About This Task

Edits the published FOUNDATIONS directly: tokens (palette/typography/spacing/iconography) and voice/tone. These are small, structured, org-wide files; unlike elements/templates they have no draft stage — changes are confirmation-gated, attributed, and logged. Conventions: `internal/conventions.md`.

### Inputs
Which foundation; the changes.

### Outputs
- `/shared/brand-book/tokens/{set}.json` and/or `/shared/brand-book/voice/voice.md`, `voice/tone-modifiers.json`
- Activity-log event.

## Workflow

### Step 1: Read Current State
`aifs_read` the target file(s); show current values (or "undefined — this will create it").

### Step 2: Compose the Change
Collect changes conversationally (palette values per context, font stacks with fallbacks, voice description in natural language, tone modifiers keyed by artifact_type). Show a before/after diff. Warn when changing a token referenced by published elements (grep the published elements for `{token:...}` refs to the changed keys): "N elements reference this token and will pick the change up automatically."

### Step 3: Write (revision-aware)
On explicit confirmation: `aifs_stat` → `aifs_write` with `if_revision` (retry on `REVISION_CONFLICT`, cap 5). Append attributed event to `/shared/brand-book/activity-log.jsonl` (same revision-aware pattern): `{type:"brand_foundation_updated", target, author_hash, author_name, date, summary}`.

**On write failure:** report which file failed and current state; do not retry blindly.

## Directives

### Behavior
This is the highest-blast-radius task in the collection — token changes propagate to every consuming artifact from the next composition on. Say so in the confirmation.

### Constraints
Foundations only — never elements/templates (their tasks own them). Open-commons governance: any member CAN run this; the confirmation names the org-wide effect and the write is attributed.

### Edge Cases
First-ever run (no files): create with the documented schema shapes. Concurrent edit conflict after retries → surface, show both versions, ask.
