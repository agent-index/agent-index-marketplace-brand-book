---
name: get-asset
type: task
version: 1.0.0
collection: brand-book
description: Provider op — returns a named brand asset's metadata and content reference. Read-only.
stateful: false
produces_artifacts: false
produces_shared_artifacts: false
implements:
  capability: brand-book
  operation: get-asset
  capability_version: ">=1.0.0"
dependencies:
  skills: []
  tasks: []
external_dependencies: []
reads_from: "/shared/brand-book/"
writes_to: null
---
## About This Task

Provider operation for the `brand-book` capability (optional op). Pure read; assets are addressed by registered NAME, never by path.

### Inputs
`asset_name` (string).

### Outputs
`{found, asset, error}` per the capability type.

## Workflow

### Step 1: Read Asset Metadata

`aifs_read("/shared/brand-book/assets/{asset_name}/asset.json")`. `FILE_NOT_FOUND` → `{found: false, asset: null, error: null}`.

### Step 2: Return

Return `{found: true, asset: {name, kind, format, remote_path: "/shared/brand-book/assets/{asset_name}/{file}", dimensions, usage_rules}}`. The consumer fetches the binary itself via `aifs_read` on `remote_path` when embedding (and respects `usage_rules` — minimum size, clear space, backgrounds).

## Directives

### Behavior
Mechanical. Metadata only; this op never returns binary content inline.

### Constraints
Never write. Slug-only names.

### Edge Cases
asset.json present but binary missing → `{found: true, asset: ...}` with `warnings: ["binary missing at <remote_path>"]` so the consumer can degrade.
