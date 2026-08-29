# Rebar AutoDim — System Invariants

This document owns the cross-cutting rules that must remain true regardless of implementation details.

## Core invariants

### Source model is read-only

Rebar AutoDim may read reinforcement, grid and view data, but it must not change reinforcement design data or structural grid geometry.

```text
Revit structural model
→ authoritative source evidence

Rebar AutoDim
→ generated annotation layer only
```

### Active view defines annotation meaning

Width, height, left/right/above/below and drawing-space placement are interpreted in the active supported view, not in global project X/Y axes.

### Uncertain geometry is not approximated

If a zone cannot be interpreted safely as a supported rectangle, the plugin skips that zone.

> Missing output is preferable to an annotation that misrepresents the model.

### One source zone has one current plugin-owned result

Repeated execution replaces the previous generated annotation result for the source zone instead of appending another independent set.

### Equivalent input produces equivalent output

For the same source model state and the same active view, repeated execution must converge to an equivalent logical annotation result.

### Grid references are directional

The nearest grid globally is not necessarily the valid grid for a side. Left, right, above and below are resolved independently.

### Overall dimensions and grid offsets have different completeness rules

For a valid supported rectangular zone:

- overall width is required;
- overall height is required;
- a grid offset is required only when a valid non-coincident grid reference exists for that side.

The absence of a grid on one side does not invalidate the whole zone.

### Plugin-owned writes must be transaction-safe

A failed write transaction must not leave a partially committed annotation result.

### Zone failures should remain local

Where the Revit API permits safe isolation, failure of one zone must not invalidate independently processable zones on the same view.

### Revit owns native validity

The plugin may decide which semantic target should be dimensioned, but Autodesk Revit remains authoritative for whether a concrete `Reference`, dimension line, element creation or transaction is valid.

### User review remains outside automation authority

The plugin automates deterministic annotation work. The structural engineer still reviews the resulting drawing.

## Boundary summary

```text
Revit owns
→ source model state
→ view state
→ native reference validity
→ transaction validity

Rebar AutoDim owns
→ supported-zone interpretation
→ normalized zone meaning
→ directional reference policy
→ placement policy
→ generated-result ownership
```

## Legacy traceability

These invariants absorb the cross-cutting meaning previously distributed across `BR-*`, `FR-*`, `NFR-*` and `AC-*` artifacts. Detailed historical mapping lives in [`../evidence/legacy-traceability.md`](../evidence/legacy-traceability.md).
