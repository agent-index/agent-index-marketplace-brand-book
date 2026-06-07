---
name: preview-brand
type: task
version: 1.0.0
collection: brand-book
description: Render a sample artifact exercising the current brand (all starter elements + a chosen template, dummy slot values) so the design team can iterate without running other collections.
stateful: false
produces_artifacts: true
produces_shared_artifacts: false
dependencies:
  skills: []
  tasks: []
external_dependencies: []
reads_from: "/shared/brand-book/"
writes_to: null
---
## About This Task

The designer's feedback loop. Produces a LOCAL sample artifact; touches nothing shared.

### Inputs
Format (markdown|docx|pptx|html|pdf), optional template slug (default: a built-in sample composition covering all 14 starter elements).

### Outputs
- `members/{member_hash}/brand-book/previews/preview-{date}-{format}.{ext}` (LOCAL, native file tools)

## Workflow

### Step 1: Gather
Run the same fetch a consumer would: get-brand-guidelines (all sections, the chosen format), each element via get-element (note which return rendering_missing), the template via get-template if named (fill its slots with clearly-fake dummy values: "CLIENT-LOGO-PLACEHOLDER" box, "Client Name Ltd.").

### Step 2: Compose
Produce a sample artifact with realistic dummy content exercising every fetched element; apply voice to the sample prose; honor template sections/slots when a template is in play. Where rendering_missing: render natively and annotate inline ("[no {format} rendering for 'kpi-card' — native default shown]").

### Step 3: Write + Report
Write locally (use the appropriate document skill for docx/pptx/pdf). Report: which elements rendered from the brand, which fell back, which token references resolved/unresolved.

## Directives

### Behavior
The annotations ARE the product — the designer needs to see exactly what is and isn't defined yet.

### Constraints
Local writes only. Dummy slot values must be unmistakably fake.

### Edge Cases
Empty brand book → produce the all-native sample with a friendly "nothing defined yet — here's what the 14 starter elements look like unbranded" framing.
