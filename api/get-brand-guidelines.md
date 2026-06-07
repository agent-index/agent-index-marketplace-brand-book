---
name: get-brand-guidelines
type: task
version: 1.0.0
collection: brand-book
description: Provider op — returns the brand guidelines (tokens, voice, structure) relevant to a composition context. Read-only; consumers call it before composing an artifact.
stateful: false
produces_artifacts: false
produces_shared_artifacts: false
implements:
  capability: brand-book
  operation: get-brand-guidelines
  capability_version: ">=1.0.0"
dependencies:
  skills: []
  tasks: []
external_dependencies: []
reads_from: "/shared/brand-book/"
writes_to: null
---
## About This Task

Provider operation for the `brand-book` capability (required op). Pure read; no member interaction, no side effects. Invoked by consumer collections per `templates/resolve-capability.md` — not normally by members directly.

### Inputs
`artifact_type` (string|null), `format` (markdown|docx|pptx|html|pdf), `sections` (array of "voice"|"visual"|"structure", default all).

### Outputs
Structured return per the capability type: `{guidelines, brand_usage, brand_book_version, error}`.

## Workflow

### Step 1: Read Sources

All reads via `aifs_read` (remote):
- Requested sections include "visual" → `/shared/brand-book/tokens/palette.json`, `tokens/typography.json`, `tokens/spacing.json`, `tokens/iconography.json` (each optional — missing file = that token set undefined).
- "voice" → `/shared/brand-book/voice/voice.md` and `voice/tone-modifiers.json`; when `artifact_type` is given, select the matching tone modifier entry.
- "structure" and `artifact_type` given → `/shared/brand-book/templates/{artifact_type}.json` structural rules if such a template exists (this is a convenience surface; full templates come from `get-element`/`get-template`).

### Step 2: Read Provider Config

`aifs_read("/org-config.json")` → `capability_providers["brand-book"].providers[0].provider_config.brand_usage` → the `brand_usage` return field. Read this collection's version from `/brand-book/collection.json` → `brand_book_version`.

### Step 3: Assemble and Return

Filter token values to the requested `format` where token sets carry per-context values (e.g., palette hex for html/markdown, theme-color guidance for docx/pptx). Return the structured object. 

**On any read failure:** return `{guidelines: null, error: "<specific file + reason>"}` — never halt; the CONSUMER owns fallback behavior.

## Directives

### Behavior
Mechanical data fetch. No interpretation beyond section filtering and format selection. Empty brand book (no files yet) is NOT an error: return `{guidelines: {}, ...}` with a note field per section that is undefined — consumers then use native defaults.

### Constraints
Never write anything. Never prompt the member. Never expose raw Drive paths in returns beyond the documented remote paths.

### Edge Cases
Unknown `format` value → error naming the supported enum. `sections` containing unknown names → ignore unknown, process known, note ignored.
