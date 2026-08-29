# System View

This area owns the **cross-system meaning and synthesis** of Rebar AutoDim.

Rebar AutoDim is a deterministic annotation system running inside Autodesk Revit. It interprets existing structural evidence in the active-view context and creates one regenerable native annotation result per supported source zone without changing reinforcement design data.

## System boundary

```text
Structural Engineer
        ↓ run / review
Rebar AutoDim
        ↕
Autodesk Revit 2025
├── active view           read
├── Area Reinforcement    read
├── structural grids      read
├── native annotations    plugin-owned write
└── transactions          host authority
```

Revit owns source model/view state and native API validity. Rebar AutoDim owns the narrow analytical transformation from that evidence to generated annotation intent.

## Responsibility route

```text
Execution Context
→ Geometry
→ References
→ Layout
→ Annotations
→ Regeneration
        ↕
Revit Host Boundary
```

- [`execution-context/`](../execution-context/) — where the command may run and what enters analysis;
- [`geometry/`](../geometry/) — what a supported zone means in view space;
- [`references/`](../references/) — what boundaries/grids should be dimensioned;
- [`layout/`](../layout/) — where intended dimensions are placed;
- [`annotations/`](../annotations/) — what constitutes one generated result;
- [`regeneration/`](../regeneration/) — which result is current across reruns;
- [`revit-boundary/`](../revit-boundary/) — how intent crosses the host/API boundary safely;
- [`evidence/`](../evidence/) — traceability and observed implementation outcome.

## Canonical system documents

### [`invariants.md`](invariants.md)
Cross-cutting rules that must survive implementation refactoring.

### [`processing-outcomes.md`](processing-outcomes.md)
Command, zone and annotation-target outcome semantics, including the distinction between optional output and failed realization.

### [`processing-flow.md`](processing-flow.md)
End-to-end synthesis showing handoffs between responsibility owners.

## Central distinctions

```text
source structural model
!= plugin-generated annotation layer

model XYZ
!= active-view annotation frame

semantic target
!= Revit Reference representation

missing optional grid
!= annotation failure

valid zone
!= successful committed annotation result

rerun
!= append another result
```

## Verification principle

A good local model is not enough. For a representative zone, the full chain must remain coherent:

```text
eligible source
→ trustworthy ZoneGeometry
→ correct semantic targets
→ deterministic placement
→ complete intended result
→ valid Revit realization
→ one current owned result
```

Any contradiction reopens the local owner that made the invalid claim.

## Migration audit

The old `01–09` document-oriented structure has been decomposed into these canonical owners. Historical identifier coverage and removal status are recorded in [`legacy-knowledge-map.md`](legacy-knowledge-map.md) and [`../evidence/legacy-traceability.md`](../evidence/legacy-traceability.md).
