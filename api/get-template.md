---
name: get-template
type: task
version: 1.0.0
collection: brand-book
description: Provider op — returns a named artifact template (composition + declared slots) for a format. Read-only.
stateful: false
produces_artifacts: false
produces_shared_artifacts: false
implements:
  capability: brand-book
  operation: get-template
  capability_version: ">=1.0.0"
dependencies:
  skills: []
  tasks: []
external_dependencies: []
reads_from: "/shared/brand-book/"
writes_to: null
---
## About This Task

Provider operation for the `brand-book` capability (optional op). Pure read.

### Inputs
`template_name` (string), `format` (markdown|docx|pptx|html|pdf).

### Outputs
`{found, template, slots, error}` per the capability type.

## Workflow

### Step 1: Read the Template

`aifs_read("/shared/brand-book/templates/{template_name}.json")`. `FILE_NOT_FOUND` → `{found: false, template: null, slots: [], error: null}`.

### Step 2: Assemble

Return `{found: true, template: {sections, per_format.{format} notes merged, display_name, version, data_binding, marker_fills, action_contract, surfaces}, slots: <declared slots array>}`. The v1.1+ fields (`data_binding`, `marker_fills`, `action_contract`, `surfaces`) pass through verbatim when present and are absent on plain document templates — see internal/conventions.md > 'Interactive templates & action markers'. Slot definitions pass through verbatim — placement/sizing/clear_space are instructions for the consumer's composition. Slot VALUES never appear here.

## Directives

### Behavior
Mechanical read. Element references inside sections are names only — the consumer fetches each via `get-element` as needed.

### Constraints
Never write. Slug-only template names.

### Edge Cases
Corrupt JSON → `{found: false, error: ...}`. Template referencing element slugs that don't exist is NOT validated here (publish-time concern); pass through.
