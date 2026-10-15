---
name: share-draft
type: task
version: 1.1.1
collection: brand-book
description: Share a draft element or template with specific reviewers (read) or collaborators (read+write) via owner-confirmed, verified grants (core permission-changes procedure).
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
Member's draft space via `id:{member_folder_id}` anchor. `aifs_stat` the draft folder and record **both** its own Drive ID (`id` → `draft_folder_id`) **and its home drive (`drive_id` → `item_drive_id`)**. Grants and the pointer's `location.folder_id` use the FOLDER ID, never a name path. The draft lives on the owner's personal drive, so `item_drive_id` is what lets a recipient open it cross-drive (C.1.3 `crossdriveread`): a bare `id:{draft_folder_id}` resolves against the *recipient's* drive and returns PATH_NOT_FOUND on OneDrive even with the grant in place.

### Step 2: Compose Grants
Map: reviewer → `reader`, collaborator → `writer`.

**Recipients.**
- A person in `members-registry.json` → their `sharing_identity`. Read the registry by id anchor: org-config `resource_ids.members_registry` (`aifs_read("id:{members_registry_id}")`); OneDrive: read `/members-registry.json` by path instead — a bare `id:` resolves against your own drive there (core standards § Addressing, finding 63). On gdrive a by-path read of `/members-registry.json` returns FILE_NOT_FOUND for non-Drive-members; that is an addressing artifact, not absence.
- A name that doesn't resolve → ask for their email address. Never guess.
- An address outside the registry is allowed. The permission-changes table marks it **outside your org**, and it's the owner's call. Tell the owner the recipient opens it from their storage app's "Shared with me", because the brand-book index is for org members.

### Step 3: Owner Applies (gate)
Follow `/agent-index-core/internal/permission-changes.md` with `calling_task: "share-draft"`, `purpose: "Share draft {kind} '{name}'"`, `confirmer: "owner"`, `mode: "independent"`, and one `share` change per person:

```json
{ "op": "share", "resource": "id:{draft_folder_id}", "resource_label": "Draft {kind} '{name}'",
  "is_folder": true, "recipient": "{sharing_identity or email}", "role": "reader | writer",
  "reason": "You asked to share your draft with {person}", "source": "member_request" }
```

The OWNER sees the exact table and confirms it, and each grant is applied under their credentials and verified.

**Gate:**
- **No row satisfied** → report what didn't take effect (the procedure's summary lines); write NOTHING.
- **Some rows satisfied** (`partial`) → continue with ONLY those, and report the rest with an offer to re-run.

### Step 4: Pointer (after the gate)
Write nothing before the procedure returns.

**Building the scope.** Re-read `aifs_get_permissions(id:{draft_folder_id})` after the procedure returns and build `scope` from that fresh read: **explicit grants only** (ignore inherited entries), **excluding the owner**; `writer` → `collaborators`, `reader`/`commenter` → `readers`. A row that is not `applied` or `skipped_noop` is never added on the strength of the table; only what the fresh read shows counts.

- **First share** → write the pointer per conventions, with that scope and `location: {folder_id, item_drive_id}` from Step 1.
- **Later shares** → overwrite with the rebuilt scope and `last_updated`.
- **Nothing to change** (the procedure returns `nothing_to_change`) → if a pointer exists, still overwrite it with the rebuilt scope and `last_updated`.

**Every pointer overwrite** (share, unshare, nothing-to-change) sets `location.item_drive_id` from Step 1's stat. That backfills pointers written before 1.2.0, which have only `folder_id`.

Confirm to the member with the sharing vocabulary ("Jeff can now read your draft 'proposal' template").

**Unshare** (member asks to remove someone): follow the procedure with `purpose: "Remove access to draft {kind} '{name}'"` and one change per person (each removal is confirmed individually):

```json
{ "op": "unshare", "resource": "id:{draft_folder_id}", "resource_label": "Draft {kind} '{name}'",
  "is_folder": true, "recipient": "{sharing_identity or email}",
  "reason": "You asked to remove {person}'s access", "source": "member_request" }
```

Then rebuild the scope from a fresh `aifs_get_permissions(id:{draft_folder_id})` read exactly as in "Building the scope" (explicit grants only, owner excluded, writer → collaborators, reader/commenter → readers), never from the list you meant to remove. Overwrite the pointer with it (and `item_drive_id`). Set scope `"revoked"` only when nobody other than the owner remains. A declined or failed removal leaves that person in scope, because the fresh read still shows them.

## Directives

### Behavior
Surface WHO can already see the draft before changing anything: read `aifs_get_permissions(id:{draft_folder_id})` (explicit grants, owner excluded), not the pointer, which can be stale.

### Constraints
Owner-confirmed grants ONLY: never admin-as-proxy, and never call `aifs_share`/`aifs_unshare`/`aifs_transfer_ownership` outside the core permission-changes procedure. Pointer is overwrite-only. Never grant on the member's whole brand-book dir — only the specific draft folder.

### Edge Cases
Sharee not in members-registry → ask for their email; outside-org addresses are allowed and flagged. A row the backend rejects (e.g. the Workspace blocks external sharing) is `failed` with the reason; relay it. Draft already published → note that the published copy is org-readable already; sharing the draft only affects the draft.
