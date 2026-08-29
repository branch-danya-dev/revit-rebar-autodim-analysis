# Rebar AutoDim — Reference Resolution Policy

This document owns the question:

> **What should a generated dimension reference, before Revit-specific realization is considered?**

The answer is semantic first, API-specific second.

## Two layers of reference

```text
Semantic target
→ what system fact should be dimensioned?

Technical Revit Reference
→ what concrete native reference can Revit accept?
```

These layers must not be collapsed.

## Zone-boundary targets

A valid normalized zone always has four semantic boundaries:

```text
Left
Right
Bottom
Top
```

Overall width means `Left ↔ Right`.

Overall height means `Bottom ↔ Top`.

Whether those boundaries are represented by native source references or supporting detail geometry is a Revit-boundary realization concern.

## Structural grid evidence

Visible structural grids are external reference candidates.

Before selection they are interpreted in the active-view frame and classified by orientation:

```text
vertical grid
→ approximately constant u

horizontal grid
→ approximately constant v
```

Equivalent positions may be deduplicated where multiple Revit elements represent the same effective annotation position.

## Directional selection

Grid selection is independent for each side.

For normalized bounds `L`, `R`, `B`, `T`:

```text
Left
→ nearest valid vertical grid where grid.u < L

Right
→ nearest valid vertical grid where grid.u > R

Below
→ nearest valid horizontal grid where grid.v < B

Above
→ nearest valid horizontal grid where grid.v > T
```

A grid on the wrong side is invalid for that directional target even when it is globally closer.

> **Nearest valid directional grid != globally nearest grid.**

## Optionality

A structural grid target is optional.

No valid grid on one side means:

```text
semantic target = NOT_APPLICABLE
```

It does not invalidate the zone and does not create an artificial substitute reference.

## Coincident grid

If the selected grid coincides with the relevant zone boundary within tolerance:

```text
offset ≈ 0
→ grid-offset dimension not required
```

This is `NOT_REQUIRED`, not a failed dimension.

## Technical realization

Once semantic targets are known, [`revit-boundary/`](../revit-boundary/README.md) resolves usable native references.

For zone boundaries this may require supporting detail curves:

```text
semantic zone boundary
→ normalized geometry
→ supporting detail curve if needed
→ stable Revit Reference
```

Structural grids may use their native references when Revit accepts them in the active view.

The workaround does not become the semantic owner.

## Output model

Conceptually:

```text
ReferencePlan
├── WidthTarget: Left ↔ Right
├── HeightTarget: Bottom ↔ Top
├── LeftGrid?: grid
├── RightGrid?: grid
├── AboveGrid?: grid
└── BelowGrid?: grid
```

Each grid entry may be absent by design.

## Verification

Reference resolution is correct when:

- no grid from the opposite side is selected;
- the selected grid is the nearest valid one for that side;
- missing sides remain optional;
- coincident grids do not produce zero-length dimensions;
- semantic selection does not depend on which API workaround later realizes the reference.

## Legacy anchors

This owner absorbs:

- `BR-005`, `BR-006`, `BR-007`, `BR-008`, `BR-009`, and the semantic part of `BR-012`;
- `FR-007`, `FR-008`, `FR-011`, semantic part of `FR-014`;
- `AC-006`, `AC-007`, `AC-008`, `AC-009`, `AC-010`.
