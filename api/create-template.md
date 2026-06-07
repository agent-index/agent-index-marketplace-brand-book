---
name: create-template
type: task
version: 1.0.0
collection: brand-book
description: Draft a new artifact template (composition + slots) in the author's private space.
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

Starts a new artifact TEMPLATE draft (schema: `lib/schemas/template-schema.json`). Conventions: `internal/conventions.md`.

### Inputs
Template name/artifact type, sections, element placements, slots.

### Outputs
- `id:{member_folder_id}/brand-book/templates/{slug}/template.json`

## Workflow

### Step 1: Identify
As create-element Step 1, against `templates/`.

### Step 2: Compose
Collect: artifact_type; sections[] (name, required?, which elements — offer published + starter slugs); slots[] — for each: name, kind (image|text), placement, sizing, clear_space, required?. Remind: slot VALUES come from consumers at compose time (e.g., a client logo from a client-intelligence instance) — the template only defines WHERE and HOW BIG. Per-format notes optional. Show assembled template.json; confirm.

### Step 3: Write
`aifs_write` draft via `id:` anchor. Same confirmation/next-steps pattern as create-element.

## Directives

### Behavior
Walk designers through slots carefully — placement/sizing/clear-space language should be format-portable (e.g., "top-right of cover, max height 18mm" not pixel-absolute).

### Constraints
As create-element. Element references are names only — do NOT inline element definitions.

### Edge Cases
References to unpublished element slugs → allowed with a note (publish-time validation will warn).
