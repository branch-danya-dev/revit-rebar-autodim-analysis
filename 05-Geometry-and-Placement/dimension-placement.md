# Rebar AutoDim — Dimension Placement

## Purpose

This document describes how Rebar AutoDim determines where generated dimension lines should be placed around a reinforcement zone.

The placement logic is deterministic and independent from reference selection.

---

## Input

The placement stage receives:

- normalized `ZoneGeometry`;
- overall width and height dimensions;
- available grid references;
- active-view scale and orientation.

---

## Default Placement

The default dimension layout is:

```text
              Above Grid

                  ↑

        ┌──────────────────┐
        │                  │
Left ←  │       Zone       │  → Right
Grid    │                  │    Grid
        └──────────────────┘

          Overall Width
           Below Grid

           Default rules:

Dimension	Preferred Side
Overall width	Bottom
Overall height	Right
Left grid offset	Left
Right grid offset	Right
Above grid offset	Top
Below grid offset	Bottom
Base Offset

Dimension lines are placed outside the reinforcement boundary.

Each side starts with a base offset:

Zone boundary
      │
      │  gap
      ↓
Dimension line

The offset is calculated consistently for the active view.

Parallel Dimensions

More than one dimension may use the same side.

Example:

Bottom side:

Zone
────────────────

Overall Width
────────────────

Below Grid Offset
────────────────

The dimensions must use separate offset levels.

Offset Levels

Each side maintains its own placement stack.

Zone Boundary
      │
      ├── Level 1
      │
      ├── Level 2
      │
      └── Level 3

A new dimension receives the next available level on that side.

This prevents generated dimension lines from occupying the same position.

Example

If a zone requires:

overall width;
overall height;
left grid dimension;
right grid dimension;
below grid dimension;

the result may be arranged as:

                 Overall Height
                       │
                       │
        ┌──────────────┼───────
        │              │
Left    │     Zone     │     Right Grid
Grid    │              │
        └──────────────┼───────
                       │

          Overall Width
       ───────────────────

        Below Grid Offset
       ───────────────────

The exact geometry is calculated from the zone boundaries and configured offsets.

Scale Awareness

Annotation spacing must remain visually consistent across supported view scales.

The placement engine therefore accounts for the active view scale when converting drawing-space spacing into Revit model units.

Desired paper gap
        ↓
View scale
        ↓
Model-space offset
Crop Region

If the preferred placement side conflicts with the active crop region, the system may use an alternative side where the placement policy allows it.

Example:

Preferred:
Overall Width → Bottom

Bottom unavailable
        ↓
Alternative:
Overall Width → Top

The fallback remains deterministic.

Missing Dimensions

Only dimensions that actually exist reserve placement levels.

Example:

No below grid
      ↓
No below-grid dimension
      ↓
No additional bottom level required

This prevents unnecessary spacing.

Collision Scope

The current placement logic handles predictable conflicts between dimensions generated for the same reinforcement zone.

It does not attempt to solve every possible collision with:

dimensions from neighboring zones;
manually created annotations;
tags;
text notes;
unrelated Revit elements.

A full annotation collision solver is outside the current scope.

Output

The placement stage produces the geometry required for dimension creation.

DimensionPlacement
├── Side
├── OffsetLevel
├── StartPoint
├── EndPoint
└── DimensionLine

The result is passed to the annotation-generation layer.

Summary

Dimension placement follows four principles:

place dimensions outside the zone;
use predictable default sides;
stack parallel dimensions using separate offsets;
keep placement deterministic across repeated execution.