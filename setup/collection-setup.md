---
name: collection-setup
type: collection-setup
version: 1.0.0
collection: brand-book
description: Org-level setup for the brand-book collection — enforcement switch, shared-space creation, and provider registration handoff.
upgrade_compatible: true
---

## Pre-Setup Checks

1. Verify `aifs_*` connectivity (lightweight read). On failure direct to `@ai:member-bootstrap`.
2. Verify core ≥ 3.10.0 and marketplace ≥ 2.10.0 (capability runtime). On failure: halt with "brand-book requires the capability-provider runtime — run '@ai:update' first."

## Parameters

**brand_usage** [org-mandated]
- Description: Whether collections that support the brand book MUST use published brand elements (when defined), or whether members may opt out and define personal elements.
- Ask: "When a brand element is defined, should artifact-producing collections be required to use it, or is brand usage optional for members?"
- Values: `required` | `optional`
- Default: `optional`
- Note: a single org-wide switch (V1). Either way, undefined elements always fall back to native defaults — the brand book never blocks artifact production. This value is stored in the provider registry's `provider_config` at registration (install-collection Step 5.7) so consuming collections read it from `org-config.json` without touching this collection's setup responses.

## Setup Completion

1. Create `/shared/brand-book/` with subdirectories `tokens/`, `voice/`, `elements/`, `templates/`, `assets/` and an empty `activity-log.jsonl` (via `aifs_write`).
2. Create `/shared/brand-book-index/`.
3. (install-collection Step 5.5 provisions the collaborative ACLs from `collaborative-acls.json`; Step 5.7 offers provider registration carrying `brand_usage` into `provider_config`.)
4. Confirm: "Brand book installed (empty). Design team members can start with 'edit the brand' (tokens + voice) and 'create a brand element'. Run 'preview the brand' anytime."

## Upgrade Behavior

### Preserved Responses
`brand_usage`.
### Reset on Upgrade
Nothing.
### Requires Member Attention
If `brand_usage` changes after install, re-run `@ai:install-collection brand-book` (reconfigure) so registration refreshes `provider_config`.
### Migration Notes
None (1.0.0).
