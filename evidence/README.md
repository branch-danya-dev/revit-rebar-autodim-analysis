# Evidence

This area contains **supporting evidence and migration traceability** for the real Rebar AutoDim case.

Evidence validates the system model but does not become a second owner of system rules.

## Current evidence

- [`outcome.md`](outcome.md) — before/after workflow, observed implementation result and real-world outcome;
- `before.jpg` — manual-state screenshot;
- `after.jpg` — generated-result screenshot;
- [`legacy-traceability.md`](legacy-traceability.md) — historical `BR / FR / NFR / AC` coverage mapped to current canonical owners.

## Evidence can support claims such as

- the original manual workflow existed;
- the generated annotation layout was produced inside Revit;
- the implementation exercised view-relative geometry and native references;
- regeneration behavior existed in the working solution;
- the solution was accepted and used in regular company work.

## Evidence does not own

- geometry rules;
- reference-selection authority;
- layout policy;
- annotation completeness;
- regeneration semantics;
- Revit transaction behavior.

Those meanings live with their corresponding responsibility owners.

## Core principle

```text
observed result / screenshot / historical ID / implementation fact
→ evidence

current system meaning
→ canonical responsibility owner
```

If evidence contradicts the current system model, the evidence reopens the relevant owner instead of silently creating a competing truth.
