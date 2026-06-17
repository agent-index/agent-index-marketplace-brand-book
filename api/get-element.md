---
name: get-element
type: task
version: 1.0.0
collection: brand-book
description: Provider op — returns one display element's semantic definition and its rendering for a format. Read-only.
stateful: false
produces_artifacts: false
produces_shared_artifacts: false
implements:
  capability: brand-book
  operation: get-element
  capability_version: ">=1.0.0"
dependencies:
  skills: []
  tasks: []
external_dependencies: []
reads_from: "/shared/brand-book/"
writes_to: null
---
## About This Task

Provider operation for the `brand-book` capability (required op). Pure read.

### Inputs
`element_name` (string), `format` (markdown|docx|pptx|html|pdf).

### Outputs
`{found, element, rendering_missing, error}` per the capability type.

## Workflow

### Step 1: Read the Element

`aifs_read("/shared/brand-book/elements/{element_name}.json")`.
- `FILE_NOT_FOUND` → return `{found: false, element: null, rendering_missing: false, error: null}`.
- Other read failure → `{found: false, error: "<file + reason>"}`.

### Step 2: Select the Rendering

Parse. If `renderings.{format}` exists: resolve any `{token:...}` references by reading the relevant `/shared/brand-book/tokens/*.json` file(s) and substituting values; return `{found: true, element: {semantic, display_name, version, markers, rendering: <resolved>}, rendering_missing: false}`. Pass `markers` (v1.1+) through verbatim if present — they are the element's neutral insertion points; consumers/templates populate them.

If the format key is absent: return `{found: true, element: {semantic, display_name, version, rendering: null}, rendering_missing: true}` — the consumer falls back to its native default for this element.

**On token resolution failure** (referenced token file/key missing): return the rendering with the literal `{token:...}` left in place plus a `warnings` note naming the unresolved reference. Do not fail the call.

## Directives

### Behavior
Mechanical. One element read + at most a few token reads. Cache nothing across calls (each call independent).

### Constraints
Never write. Personal (member-local) elements are NOT served here — precedence between org and personal elements is the CONSUMER's job (see the consumer boilerplate `/internal/resolve-brand.md` pattern); this op serves only the published org brand book.

### Edge Cases
`element_name` containing path separators → reject with error (slug only). Corrupt JSON → `{found: false, error: "corrupt element file <name>"}`.
