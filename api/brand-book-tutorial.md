---
name: brand-book-tutorial
type: skill
version: 1.0.0
collection: brand-book
description: Guided tour of the brand-book collection — concepts, the draft→share→publish flow, tokens vs elements vs templates, slots, and how other collections consume the brand.
stateful: false
always_on_eligible: false
dependencies:
  skills: []
  tasks: []
external_dependencies: []
---
## About This Skill

Conversational guided tour, standard two-mode convention (sequential walkthrough / targeted Q&A).

### When This Skill Is Active
Explains; never executes operations.

### What This Skill Does Not Cover
Building or publishing items (the editor tasks), provider registration (admin install flow).

## Directives

### Behavior
Adapt to the member: designers get design-language framing; developers get the capability-provider framing. Walk topics in order for the full tour; answer directly for targeted questions.

### Topics (7)
1. **What the brand book is** — the org's single source of truth for artifact look/sound/feel; consuming collections inherit it automatically.
2. **Three layers** — tokens (primitives) → elements (14 starters + custom) → templates (compositions with slots).
3. **Slots** — org-branded artifacts with counterparty content in designated places; values always come from the consuming collection's context.
4. **The lifecycle** — private draft → share with reviewers/collaborators (vocabulary: share=read, collaborator=read+write) → publish to the org (attributed, logged, versioned).
5. **required vs optional** — the org-wide brand_usage switch; personal elements and their precedence.
6. **Previewing** — preview-brand as the iteration loop.
7. **How collections consume it** — the capability provider model in one paragraph; what happens when no brand book is installed (nothing breaks).

### Constraints
Read-only. Direct members to the right task by trigger phrase at each topic's end.

### Edge Cases
Asked about a not-yet-published org brand → frame around what WILL happen once published.
