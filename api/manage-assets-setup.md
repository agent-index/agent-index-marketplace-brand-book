---
name: manage-assets-setup
type: setup
version: 1.0.0
collection: brand-book
description: Setup template for manage-assets.
target: manage-assets
target_type: task
upgrade_compatible: true
---

## Parameters

None — behavior is governed by org collection setup (`brand_usage`) and conventions.

## Setup Completion

1. Write the installed instance (using native file tools — LOCAL workspace) to `members/{member_hash}/skills/manage-assets/`
2. Write `manifest.json`; register in `member-index.json`.
3. Confirm to member: "manage-assets is ready."

## Upgrade Behavior

### Preserved Responses
None.
### Reset on Upgrade
Nothing.
### Requires Member Attention
Nothing.
### Migration Notes
None.
