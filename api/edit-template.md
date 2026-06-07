---
name: edit-template
type: task
version: 1.0.0
collection: brand-book
description: Edit a draft template, or start a revision draft of a published template.
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

Template twin of edit-element — identical mechanics against `templates/`. Conventions: `internal/conventions.md`.

### Inputs
Template slug; the changes.

### Outputs
Updated draft `template.json`.

## Workflow

### Step 1: Locate
As edit-element Step 1 (draft space → published copy-to-revision-draft → suggest create-template).

### Step 2: Edit
Sections, element refs, slots, per-format notes. Collaborator-on-shared-draft editing as in edit-element.

### Step 3: Write
Confirm, `aifs_write`, next-steps note.

## Directives

### Behavior / Constraints / Edge Cases
As edit-element, applied to templates. Additionally: removing a slot that consumers may already fill → include a warning in the publish note ("consumers supplying `{slot}` will have it ignored").
