# Reference Resolution

This area owns the canonical decision about **which references may legitimately participate in a generated dimension**.

It separates two questions that were previously easy to mix:

```text
Which structural object should be referenced?
!=
Which Revit Reference representation can be used to create the dimension?
```

## Owned knowledge

References owns:

- classification of visible structural grids in view coordinates;
- directional grid eligibility;
- nearest-valid-grid selection for `left`, `right`, `above`, `below`;
- coincident-grid semantics;
- optionality of missing directional grid references;
- the requirement that a selected semantic target must ultimately have a usable dimension reference.

It does **not** own:

- the normalized zone boundaries — [`geometry/`](../geometry/);
- dimension placement side/offset — [`layout/`](../layout/);
- how supporting Revit geometry is physically created — [`annotations/`](../annotations/) / [`revit-boundary/`](../revit-boundary/).

## Directional grid model

Grid selection is directional, not globally nearest.

```text
          Above
            ↑
            │
Left ← [ Zone ] → Right
            │
            ↓
          Below
```

For normalized boundaries `L`, `R`, `B`, `T`:

```text
Left  → nearest vertical grid where grid.u < L
Right → nearest vertical grid where grid.u > R
Below → nearest horizontal grid where grid.v < B
Above → nearest horizontal grid where grid.v > T
```

A closer grid on the wrong side is not eligible evidence for that directional dimension.

## Missing reference semantics

Each directional grid reference is optional.

```text
GridReferences
├── Left?
├── Right?
├── Above?
└── Below?
```

Missing one grid does not invalidate the zone and does not suppress overall width/height dimensions.

```text
missing right grid
→ omit right-grid offset dimension
→ keep other valid dimensions
```

## Coincident reference

If a grid coincides with the corresponding zone boundary within geometric tolerance, the semantic offset is effectively zero.

```text
grid position ≈ zone boundary
        ↓
no useful offset to communicate
        ↓
do not create zero-length dimension
```

## Semantic target vs API realization

A selected grid or zone boundary is a **semantic reference target**.

Revit dimension creation additionally requires compatible `Reference` objects.

For structural grids, a direct valid Revit reference may be available.

For zone boundaries, raw `Area Reinforcement` geometry may not expose references suitable for `NewDimension`. Supporting detail geometry may therefore be created as a technical realization of the already-owned geometric intent.

```text
semantic boundary target
        ↓
Revit-compatible reference realization
        ↓
ReferenceArray
```

The technical workaround must not change which geometry the dimension is supposed to represent.

## Failure rule

If a semantic target exists but no supported valid Revit reference can be realized, the affected dimension must be skipped rather than fabricated using a different target.

> **Reference failure may reduce the annotation result; it must not silently change its meaning.**
