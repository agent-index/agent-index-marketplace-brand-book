---
name: manage-assets
type: task
version: 1.0.0
collection: brand-book
description: Upload, name, replace, or retire brand assets (logos, icons, patterns) in the published brand book.
stateful: false
produces_artifacts: false
produces_shared_artifacts: true
dependencies:
  skills: []
  tasks: []
external_dependencies: []
reads_from: "/shared/brand-book/"
writes_to: "/shared/brand-book/assets/"
---
## About This Task

Manages NAMED brand assets. Consumers fetch by name via get-asset; names are the contract. Conventions: `internal/conventions.md`.

### Inputs
Operation (add|replace|retire|list), asset name, file (member-provided local path), usage rules.

### Outputs
- `/shared/brand-book/assets/{slug}/asset.json` + binary file
- Activity-log event.

## Workflow

### Step 1: Operation + Inventory
`aifs_list("/shared/brand-book/assets/")` → show current named assets. Determine operation.

### Step 2: Add / Replace
Collect: name (slugify; uniqueness against listing), kind (logo|icon|pattern|background|other), the local file, dimensions if known, usage_rules (min size, clear space, allowed backgrounds). **Size guard:** if file > 2MB warn about base64 transport (~4/3 inflation) and confirm; if > 8MB refuse with guidance to optimize. Write binary via `aifs_write` then `asset.json`. **Transport integrity:** after upload, `aifs_stat` size vs local size (allowing the trailing-newline variance only for text — binaries must match exactly); on mismatch, delete the bad upload (contents-first) and retry once.

### Step 3: Retire
Never hard-delete a named asset consumers may reference: set `"retired": true` + `retired_date` in asset.json (keep the binary). get-asset continues serving with a `warnings: ["retired"]` note.

### Step 4: Log
Attributed activity-log event per conventions.

## Directives

### Behavior
Names are forever-ish — renames break consumers; offer add-new + retire-old instead of rename.

### Constraints
Fonts are out of scope in V1 (typography = named stacks). No asset content in get-asset returns — metadata only.

### Edge Cases
Replacing a retired asset → un-retire on confirmation. Upload interrupted → re-run is safe (overwrite).
