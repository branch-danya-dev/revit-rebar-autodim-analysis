# Rebar AutoDim — Generated Annotation Result

This document owns **what constitutes the plugin-generated result for one eligible source zone**.

It separates semantic completeness from Revit transaction mechanics.

## Result boundary

One source `Area Reinforcement` zone maps to one current plugin-owned annotation result.

A result may contain:

- supporting detail geometry required to realize stable references;
- one overall width dimension;
- one overall height dimension;
- zero to four grid-offset dimensions;
- grouping/association elements where used;
- generation metadata needed by regeneration.

Only native Revit annotation-related elements are written. Source reinforcement and grid geometry remain unchanged.

## Mandatory content

For an eligible supported rectangular zone, a committed annotation result requires:

```text
Overall width dimension
+
Overall height dimension
```

These dimensions are part of the core annotation contract, not optional decoration.

If either mandatory target cannot be realized safely, the zone must not be reported as a complete committed annotation result.

## Conditional content

Grid-offset dimensions are conditional.

For each side:

```text
valid directional grid exists?
   no → no dimension expected
  yes
   ↓
grid coincident with zone boundary?
  yes → no offset dimension required
   no
   ↓
grid offset dimension expected
```

Therefore a committed result can be valid with fewer than four grid dimensions.

## Supporting geometry

Supporting detail curves are an implementation-facing part of the generated result only when needed to obtain stable Revit references.

They do not redefine source-zone geometry.

```text
source Area Reinforcement
→ canonical source geometry

supporting detail curve
→ plugin-owned annotation support element
```

## Native result

Generated dimensions must exist as native Revit annotation elements rather than screenshots or external overlays.

The user reviews the resulting drawing inside Revit.

## Partial-by-design vs incomplete result

These states must not be confused:

```text
No right-side grid exists
→ result can still be complete

Right-side grid exists and is required,
but native dimension cannot be created
→ intended result is incomplete
```

The first is domain optionality. The second is a realization failure.

## Readability boundary

The current system owns predictable same-zone placement readability through [`../layout/`](../layout/README.md).

It does not guarantee global collision-free drawings against arbitrary unrelated annotations.

## Result identity

The exact Revit representation may evolve, but the system needs enough stable ownership metadata to answer:

```text
Which generated elements currently belong to this source zone?
```

That ownership relation is canonical in [`../regeneration/`](../regeneration/README.md).

## Verification

A committed result is valid when:

- mandatory width and height dimensions exist;
- every applicable non-coincident selected grid has its intended offset dimension unless the zone transaction failed;
- no non-applicable grid dimension is fabricated;
- generated elements are native Revit elements;
- source structural data is unchanged;
- the result can be identified for future regeneration.

## Legacy anchors

This owner absorbs:

- `BR-004`, annotation aspect of `BR-012`, `BR-013`;
- `FR-009`, `FR-010`, `FR-011`, `FR-014`, `FR-017`, `FR-018`;
- `NFR-002`, result-readability aspect of `NFR-009`, `NFR-011`;
- `AC-003`, `AC-004`, `AC-008`, `AC-009`, `AC-010`, `AC-014`, `AC-015`.
