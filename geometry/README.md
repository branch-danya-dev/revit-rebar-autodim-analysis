# Zone Geometry

This area owns the canonical interpretation of a supported `Area Reinforcement` zone as geometry that the annotation system can reason about.

It answers:

> **What geometric zone is being dimensioned in the active view, and is that interpretation reliable enough to annotate?**

## Owned knowledge

Geometry owns:

- projection of source geometry into the execution-context view frame;
- supported rectangular-zone semantics;
- boundary extraction and supported fallback evidence;
- normalized boundaries:
  - `Left`;
  - `Right`;
  - `Bottom`;
  - `Top`;
- derived `Width`, `Height` and `Center`;
- geometry validity and rejection conditions.

It does **not** own:

- which zones are candidates for the run — [`execution-context/`](../execution-context/);
- which grids/references should be used — [`references/`](../references/);
- where dimension lines belong — [`layout/`](../layout/);
- the Revit detail curves used to realize references — [`annotations/`](../annotations/) and [`revit-boundary/`](../revit-boundary/).

## Canonical normalized model

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

All values are interpreted in active-view coordinates, not directly in global project X/Y axes.

```text
source Revit geometry
        ↓
view projection
        ↓
normalized ZoneGeometry
```

## Geometry evidence hierarchy

The preferred evidence is the actual reinforcement boundary.

```text
Area Reinforcement boundary
        ↓ usable?
       yes
        ↓
normalized zone
```

A view-specific bounding representation may be used only as an explicitly supported fallback when it preserves the required rectangular meaning.

```text
boundary unavailable
        ↓
supported fallback evidence
        ↓ valid?
      yes / no
       ↓   ↓
 normalize skip
```

Fallback is evidence, not permission to invent approximate geometry.

## Validity rules

A candidate zone is valid only when the system can establish a supported rectangle with non-zero dimensions in the active view.

Reject or skip when:

- usable boundary evidence cannot be obtained;
- width or height is effectively zero;
- the shape cannot be represented by the supported rectangular model;
- required geometry is invalid in the active view.

Core safety rule:

> **Uncertain geometry must not be converted into confident annotation.**

## Geometry vs annotation references

The normalized rectangle describes **what the zone means geometrically**.

It does not imply that its raw Revit geometry exposes references accepted by the dimension API.

```text
ZoneGeometry
!=
Revit dimensionable Reference
```

If supporting detail geometry is required to realize stable references, that is an annotation/API realization concern, not a redefinition of zone geometry.

## Reopening conditions

Geometry must be recalculated whenever source reinforcement geometry or the active-view context changes.

The previous normalized result is not canonical after such a change.
