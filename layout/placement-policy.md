# Rebar AutoDim — Annotation Placement Policy

This document owns **where intended dimensions are placed in the active view**.

It does not decide what geometry means or which grid is the correct reference.

## Inputs

Placement consumes:

- normalized `ZoneGeometry`;
- required overall dimension targets;
- available conditional grid targets;
- active-view orientation and scale;
- supported crop-region constraints where applicable.

## Default side policy

| Annotation target | Preferred side |
|---|---|
| Overall width | Bottom |
| Overall height | Right |
| Left grid offset | Left |
| Right grid offset | Right |
| Above grid offset | Top |
| Below grid offset | Bottom |

The policy is deterministic: the same logical inputs should produce equivalent placement intent.

## Placement outside the zone

Dimension lines are placed outside the normalized zone boundary using a base drawing-space gap converted to model-space distance for the active view.

```text
desired paper gap
× view scale
→ model-space offset
```

This keeps visual spacing consistent across supported scales.

## Side-local offset stacks

Several dimensions may occupy the same side.

Example on the bottom side:

```text
Zone boundary
    ↓
Level 1 → Overall width
Level 2 → Below-grid offset
```

Each side owns an independent ordered stack of occupied levels.

Only annotation targets that actually exist reserve a level.

Therefore a missing below-grid target does not create an empty spacing slot.

## Collision scope

The placement policy guarantees deterministic separation among dimensions generated for the **same source zone**.

It does not own arbitrary drawing-wide collision solving with:

- manually created dimensions;
- text notes;
- tags;
- annotations from other zones;
- unrelated model elements.

That broader optimization is outside the current system boundary.

## Crop-region adaptation

When the preferred side cannot be used under an explicitly supported crop-region rule, the system may choose a deterministic alternative side.

Fallback must remain policy-driven rather than depend on element creation order or arbitrary runtime behavior.

If no supported valid placement can be determined for a mandatory target, that becomes an annotation-realization failure rather than an invitation to place an uncertain dimension.

## Output model

Conceptually:

```text
DimensionPlacement
├── Target
├── Side
├── OffsetLevel
├── StartPoint
├── EndPoint
└── DimensionLine
```

This is placement intent. Revit-native creation remains owned by [`../revit-boundary/`](../revit-boundary/README.md).

## Verification

Placement is correct when:

- intended dimensions lie outside the source zone;
- default side rules are applied consistently;
- same-side generated dimensions use distinct offset levels;
- absent conditional dimensions do not reserve levels;
- equivalent input produces equivalent logical placement;
- supported scale changes preserve intended drawing-space spacing.

## Legacy anchors

This owner absorbs:

- `BR-010`, `BR-011`;
- `FR-012`, `FR-013`;
- `NFR-005`, placement aspect of `NFR-009`, separation aspect of `NFR-012`;
- `AC-011`, placement aspect of `AC-018`.
