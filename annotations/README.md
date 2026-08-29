# Generated Annotations

This area owns the canonical meaning of the **plugin-generated annotation result** for one supported reinforcement zone.

It answers:

> **What native annotation set should represent the calculated dimension intent in Revit?**

## Owned knowledge

Annotations owns:

- the composition of one generated annotation result;
- required overall dimensions;
- optional grid-offset dimensions;
- supporting detail geometry when needed to realize stable references;
- association of created native elements into one plugin-owned result;
- the distinction between source model data and generated annotation data.

It does **not** own:

- geometry interpretation — [`geometry/`](../geometry/);
- reference-target selection — [`references/`](../references/);
- placement policy — [`layout/`](../layout/);
- lifecycle/replacement of an older result — [`regeneration/`](../regeneration/);
- transaction semantics — [`revit-boundary/`](../revit-boundary/).

## Annotation-set model

Conceptually:

```text
GeneratedAnnotationSet
├── source Area Reinforcement identity
├── supporting detail curves, when required
├── Overall Width dimension
├── Overall Height dimension
├── Left Grid Offset? 
├── Right Grid Offset?
├── Above Grid Offset?
├── Below Grid Offset?
└── persistent generation identity/metadata link
```

The optional dimensions are absent when their reference intent is not available or not meaningful.

## Source vs generated layer

The plugin must preserve this boundary:

```text
SOURCE STRUCTURAL MODEL
Area Reinforcement
Structural Grids
        ↓ read only

REBAR AUTODIM
        ↓ writes

GENERATED ANNOTATION LAYER
Detail Curves
Dimensions
Generated Group / Ownership Metadata
```

The annotation result communicates model facts. It must not mutate the reinforcement design in order to make annotation easier.

## Supporting detail geometry

Supporting detail curves may be needed because raw `Area Reinforcement` boundaries do not always expose references accepted by the Revit dimension API.

Their purpose is technical:

```text
normalized zone boundary intent
        ↓
supporting detail curve
        ↓
stable Revit Reference
        ↓
native dimension
```

These curves do not become a second structural model and must remain clearly owned as generated annotation support.

## Result completeness

A valid zone normally requires overall width and height dimensions.

Directional grid dimensions are conditional.

```text
valid zone
→ overall width + overall height

valid directional grid reference
→ corresponding grid offset dimension

missing/invalid optional reference
→ omit only affected optional dimension
```

If a required dimension cannot be realized safely, the zone result may be considered incomplete and handled according to the failure policy rather than silently claiming success.

## Native-result invariant

The output must remain native Revit annotation that behaves as Revit annotation after creation.

The system does not render a custom overlay or external drawing layer.

## Relationship to regeneration

Annotations owns **what the current generated result means**.

Regeneration owns **which generated result is current and how the previous one is replaced**.
