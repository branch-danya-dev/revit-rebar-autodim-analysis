# Rebar AutoDim — Edge Cases

## Purpose

This document describes how Rebar AutoDim behaves when expected geometry, references or Revit API operations are unavailable.

The main rule is simple:

> Skip uncertain results instead of creating incorrect annotations.

---

## Unsupported View

If the active Revit view is not supported, processing stops before any model changes are made.

Examples:

- 3D view;
- drafting view;
- unsupported view type.

Expected result:

```text
Unsupported view
      ↓
Stop command
      ↓
No annotations created

No Reinforcement Zones

If no visible Area Reinforcement elements are found:

Supported view
      ↓
No candidate zones
      ↓
No changes
      ↓
Report result

This is treated as a valid empty result, not a processing error.

Invalid Zone Geometry

A zone is skipped when:

boundary geometry cannot be obtained;
width or height is effectively zero;
the geometry cannot be normalized into a supported rectangle.
Invalid geometry
      ↓
Skip zone
      ↓
Continue processing

No approximate dimensions are created.

Missing Grid

A structural grid may be missing on one or more sides.

Example:

Left   → found
Right  → missing
Above  → found
Below  → missing

Expected result:

Overall width
Overall height
Left grid dimension
Above grid dimension

The zone remains valid even when some external references are unavailable.

Coincident Grid

If a structural grid coincides with the corresponding zone boundary within geometric tolerance:

Grid ≈ Zone Boundary
        ↓
Offset ≈ 0
        ↓
Do not create dimension

Zero-length grid dimensions are not generated.

Invalid Dimension Reference

Revit may reject a reference combination during dimension creation.

Possible causes include:

reference is not valid in the active view;
reference geometry is incompatible;
dimension line is not valid for the supplied references.

Expected behavior:

Reference rejected
      ↓
Try supported fallback
      ↓
Still invalid?
   /          \
 Yes          No
  ↓            ↓
Skip        Create
dimension   dimension

The affected dimension should fail locally where possible.

Placement Conflict

Multiple generated dimensions may require the same side.

The plugin resolves predictable same-zone conflicts using offset levels.

Zone
 │
 ├── Level 1
 ├── Level 2
 └── Level 3

The current solution does not attempt to solve arbitrary collisions with unrelated Revit annotations.

Previous Result Missing

Generation metadata may reference annotations that were manually deleted.

Metadata exists
      ↓
Generated group not found
      ↓
Treat as already removed
      ↓
Create new result

A missing previous result must not block regeneration.

Partial Previous Result

If the user manually removes part of a generated annotation set, the plugin does not repair individual elements.

Instead:

Partial old result
      ↓
Remove remaining generated result
      ↓
Recalculate
      ↓
Create complete result
Zone-Level Failure

Failure of one zone should not stop processing of other valid zones where technically possible.

Example:

Zone A → Success
Zone B → Invalid geometry → Skip
Zone C → Success
Transaction Failure

If a transaction cannot be completed:

Write operations
      ↓
Transaction failure
      ↓
Rollback

The document must not remain in a partially committed state.

Error Handling Matrix
Condition	Action
Unsupported view	Stop command
No zones found	Complete with no changes
Invalid zone geometry	Skip zone
Missing grid	Skip corresponding grid dimension
Coincident grid	Do not create zero-length dimension
Invalid dimension reference	Skip affected dimension
Previous result missing	Regenerate normally
Partial previous result	Replace full generated result
Zone-level failure	Continue with other zones
Transaction failure	Roll back
Summary

Rebar AutoDim follows three failure-handling principles:

do not generate uncertain annotations;
keep failures local where possible;
preserve the source structural model.