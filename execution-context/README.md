# Execution Context

This area owns **where one Rebar AutoDim command may run, which source zones belong to that run, and which active-view frame downstream analysis uses**.

## Canonical question

> Which Revit context is active, what enters this execution, and may processing continue?

## Owned knowledge

- active document and active supported view;
- view-local orientation, scale and relevant crop context;
- view-scoped discovery of visible `Area Reinforcement`;
- unsupported-context stop behavior;
- valid empty result semantics;
- candidate/eligible/skip boundary at the start of processing.

## Main model

```text
active Revit view
        ↓
validate supported context
        ↓
discover visible Area Reinforcement
        ↓
0 candidates? → valid empty result
        ↓
candidate zones
        ↓
eligibility evaluation
```

The view provides the annotation frame; it does not own source structural geometry.

## Canonical detail

→ [`eligibility.md`](eligibility.md) — supported context, discovery, zone eligibility, batch semantics and legacy verification anchors.

## Does not own

- rectangular geometry proof → [`../geometry/`](../geometry/);
- grid/reference selection → [`../references/`](../references/);
- placement → [`../layout/`](../layout/);
- Revit write realization → [`../revit-boundary/`](../revit-boundary/).

Execution Context is rebuilt for every command run because active view, visibility, scale, crop and source evidence may have changed.
