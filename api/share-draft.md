---
name: share-draft
type: task
version: 1.0.0
collection: brand-book
description: Share a draft element or template with specific reviewers (read) or collaborators (read+write) via owner-applied verified grants.
stateful: false
produces_artifacts: false
produces_shared_artifacts: true
dependencies:
  skills: []
  tasks: []
external_dependencies: []
reads_from: "/shared/brand-book/"
writes_to: "/shared/brand-book-index/"
---
## About This Task

Owned-content sharing for brand DRAFTS — the strategy-collection pattern applied to brand items. Sharing vocabulary: "share with X" = X can read; "make X a collaborator" = X can read + write. Conventions: `internal/conventions.md`.

### Inputs
Draft slug (element or template), people, role per person.

### Outputs
- Grants on the draft folder (owner-applied, verified)
- `/shared/brand-book-index/{owner_hash}-{kind}-{slug}.json` pointer (on first share)

## Workflow

### Step 1: Locate the Draft
Member's draft space via `id:{member_folder_id}` anchor. Resolve the draft folder's own Drive ID (`aifs_stat`) — grants and pointer `location.folder_id` use the FOLDER ID, never a name path.

### Step 2: Compose Grants
Resolve each person via `members-registry.json` (remote). Map: reviewer → `reader`, collaborator → `writer`. Build one permission-change-helper spec (resources as `id:{draft_folder_id}`, version per helper requirements, helper-go ≥ 0.4.1).

### Step 3: Owner Applies (HARD GATE)
The OWNER reviews and Accepts the helper page; the apply runs under their token. Gate: proceed only when the outcome file reads `"applied"` — or, if the outcome is missing/ambiguous, an independent `aifs_get_permissions(id:{draft_folder_id})` shows the grants. Anything else → report exactly what was/wasn't applied; write NOTHING.

### Step 4: Pointer (after the gate)
First share → write the pointer per conventions (scope `{readers:[...], collaborators:[...]}`); later shares → overwrite with updated scope + `last_updated`. Confirm to the member with the sharing vocabulary ("Jeff can now read your draft 'proposal' template").

**Unshare** (member asks to remove someone): revoke via helper (same gate), overwrite pointer scope; if nobody remains, scope `"revoked"`.

## Directives

### Behavior
Surface WHO can already see the draft (current pointer scope) before changing anything.

### Constraints
Owner-applied grants ONLY — never admin-as-proxy, never raw `aifs_share` in this workflow. Pointer is overwrite-only. Never grant on the member's whole brand-book dir — only the specific draft folder.

### Edge Cases
Sharee not in members-registry → name the gap, suggest the admin check invite status; skip that person. Outcome `terminated`/missing but get_permissions confirms → proceed (F14 contract). Draft already published → note that the published copy is org-readable already; sharing the draft only affects the draft.
