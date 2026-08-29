# Rebar AutoDim — Normalized Zone Geometry

This document owns the **semantic geometric representation of one supported reinforcement zone**.

It does not own Revit annotation creation or dimension-line placement.

## Input evidence

Geometry interpretation consumes:

- source `Area Reinforcement` identity;
- boundary geometry visible/available in the active view;
- the active-view coordinate frame from [`execution-context/`](../execution-context/README.md).

## View-space coordinates

All annotation geometry is interpreted relative to the active view.

```text
u → horizontal direction on the view
v → vertical direction on the view
w → depth relative to the view plane
```

Revit XYZ points are projected into this frame before width, height or directional boundaries are derived.

```text
Model XYZ
→ active-view frame
→ (u, v, w)
```

Therefore:

```text
project X/Y
!=
annotation horizontal/vertical
```

## Boundary evidence

Preferred evidence is the actual reinforcement boundary geometry.

```text
Area Reinforcement
→ boundary curves / points
→ view-space points
```

A view-specific bounding representation may be used only as an explicitly supported fallback when it still produces a trustworthy rectangular interpretation.

If neither evidence path can establish valid geometry, the zone is rejected.

## Rectangle normalization

For all accepted boundary points in `(u, v)`:

```text
Left   = min(u)
Right  = max(u)
Bottom = min(v)
Top    = max(v)

Width  = Right - Left
Height = Top - Bottom
```

The resulting canonical local model is:

```text
ZoneGeometry
├── Left
├── Right
├── Bottom
├── Top
├── Width
├── Height
└── Center
```

Downstream logic uses this normalized model rather than repeatedly reinterpreting raw Revit geometry.

## Validity rules

A zone is not accepted when:

- usable boundary evidence cannot be obtained;
- width or height is effectively zero within geometric tolerance;
- the geometry cannot be represented by the supported rectangular model;
- the geometry is invalid in the active view context.

```text
uncertain geometry
→ no approximate ZoneGeometry
→ zone skipped
```

## What Geometry owns

Geometry owns:

- view-space boundary meaning;
- normalized rectangle;
- width and height values;
- geometric validity for the supported rectangular model.

Geometry does **not** own:

- which structural grid is selected;
- where dimensions are placed;
- which technical Revit `Reference` is used;
- transaction behavior;
- previous generated-result ownership.

## Verification

For any valid zone:

1. width and height are derived from the active view frame;
2. `Left < Right` and `Bottom < Top` within tolerance;
3. the normalized rectangle represents the source boundary sufficiently for supported annotation behavior;
4. repeated analysis of unchanged evidence produces equivalent `ZoneGeometry`.

## Legacy anchors

This owner absorbs:

- `BR-002`, `BR-003`;
- `FR-003`, `FR-004`, `FR-005`, `FR-006`;
- `NFR-003`;
- `AC-002`, `AC-003`, `AC-004`, `AC-005`.

`AC-003/004` also depend on [`annotations/result-model.md`](../annotations/result-model.md), because Geometry owns width/height meaning while Annotations owns whether dimensions are actually created.
