---
name: edit-template-setup
type: setup
version: 1.0.0
collection: brand-book
description: Setup template for edit-template.
target: edit-template
target_type: task
upgrade_compatible: true
---

## Parameters

None — behavior is governed by org collection setup (`brand_usage`) and conventions.

## Setup Completion

1. Write the installed instance (using native file tools — LOCAL workspace) to `members/{member_hash}/skills/edit-template/`
2. Write `manifest.json`; register in `member-index.json`.
3. Confirm to member: "edit-template is ready."

## Upgrade Behavior

### Preserved Responses
None.
### Reset on Upgrade
Nothing.
### Requires Member Attention
Nothing.
### Migration Notes
None.
