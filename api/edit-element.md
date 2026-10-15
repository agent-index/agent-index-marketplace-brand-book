---
name: edit-element
type: task
version: 1.0.2
collection: brand-book
description: Edit a draft element in the author's space, or start a revision draft of a published element.
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

Edits element DRAFTS. Editing a PUBLISHED element = creating a revision draft from the published content, then publishing it again. Conventions: `internal/conventions.md`.

### Inputs
Element slug; the changes.

### Outputs
Updated draft `element.json` in the member's space.

## Workflow

### Step 1: Locate
Check the member's draft space (`id:` anchor) for the slug. If absent, check `/shared/brand-book/elements/{slug}.json`: if published → confirm "create a revision draft from the published version?"; copy content into a new draft on yes. If neither → suggest create-element.

### Step 2: Edit
Apply requested changes (semantic, renderings, token refs). If the member is a COLLABORATOR on someone else's shared draft (resolved via the index pointer's `location`; open it via the cross-drive anchor `id:{item_drive_id}:{folder_id}/element.json` when `item_drive_id` is present, else the bare `id:{folder_id}/element.json`), edit THAT draft in place — collaborator writes are the point of collaboration; the activity is visible to the owner via Drive. Skip pointers with `status: "published"` (their location is a commons path, not a draft). If the bare `id:{folder_id}/element.json` read returns PATH_NOT_FOUND for a pointer without `item_drive_id` (written before brand-book 1.2.0), stop and ask the draft's owner to re-run share-draft, which backfills `item_drive_id`. Never try another connector.

### Step 3: Write
Confirm before writing. `aifs_write` to the draft path. For a collaborated draft, write through the **same base used to read it in Step 2**: `id:{item_drive_id}:{folder_id}/element.json` (or the bare `id:{folder_id}/element.json` for an old pointer), never the member's own draft path `id:{member_folder_id}/brand-book/elements/…`, which would create a stray copy in the collaborator's space. For revision drafts note: "publish when ready to update the org element (its version will bump)."

**On write failure:** surface path + reason.

## Directives

### Behavior
Show a before/after of changed renderings.

### Constraints
Never write published files. Never change `version` in drafts (publish computes it). No permission changes here (share-draft's job).

### Edge Cases
Draft slug ambiguity between own draft and a collaborated draft → ask which. Corrupt draft JSON → offer to re-initialize from published version or from scratch.
