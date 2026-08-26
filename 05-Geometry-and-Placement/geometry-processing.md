# Rebar AutoDim — Geometry Processing

## Purpose

This document describes how Rebar AutoDim converts raw `Area Reinforcement` geometry into a normalized rectangular representation used by the dimensioning logic.

---

## Input

For each supported reinforcement zone, the geometry processor receives:

- the source `Area Reinforcement` element;
- active view context;
- visible boundary geometry.

---

## View Coordinate System

Geometry is processed relative to the active view.

The local coordinate system is:

```text
u → horizontal direction on the view
v → vertical direction on the view
w → depth relative to the view plane

Revit XYZ points are projected into this system before width, height and placement are calculated.

Model XYZ
    ↓
Project to active view
    ↓
(u, v, w)

This avoids dependence on global project X/Y orientation.

Boundary Extraction

The preferred source is the actual reinforcement boundary.

Area Reinforcement
        ↓
Boundary Curves
        ↓
Curve Endpoints / Points
        ↓
View Coordinates

If usable boundary geometry cannot be obtained, the implementation may use the view-specific bounding box as a fallback.

If neither source produces valid geometry, the zone is skipped.

Rectangle Normalization

All boundary points are projected into (u, v) coordinates.

The zone limits are then calculated:

Left   = min(u)
Right  = max(u)

Bottom = min(v)
Top    = max(v)

Result:

                Top

        ┌────────────────┐
        │                │
 Left   │      Zone      │   Right
        │                │
        └────────────────┘

              Bottom
Width and Height

The normalized dimensions are:

Width  = Right - Left
Height = Top - Bottom

These values represent visual width and height on the active Revit view.

They are not derived directly from global model axes.

Normalized Zone Model

The geometry processor produces a simplified representation:

ZoneGeometry
├── Left
├── Right
├── Bottom
├── Top
├── Width
├── Height
└── Center

All later processing uses this model instead of repeatedly interpreting raw Revit geometry.

Supporting Reference Geometry

Native Area Reinforcement geometry does not always provide references that can be used reliably by Revit dimension creation.

Where required, the normalized rectangle is converted into four supporting detail curves:

        Top Reference
     ─────────────────

Left │                  │ Right
Ref  │       Zone       │ Ref
     │                  │

     ─────────────────
       Bottom Reference

These curves provide predictable references for overall width and height dimensions.

Invalid Geometry

A zone is rejected when:

valid boundaries cannot be obtained;
width or height is effectively zero;
the geometry cannot be represented as a supported rectangle;
required geometry is invalid in the active view.
Invalid geometry
       ↓
Skip zone
       ↓
Continue processing

The system does not create approximate dimensions for uncertain geometry.

Output

The geometry-processing stage produces:

Raw Area Reinforcement
        ↓
Normalized ZoneGeometry
        +
Stable Zone References

These outputs are passed to grid selection and dimension-placement logic.

Summary

The geometry layer reduces Revit-specific geometry into a simple view-based rectangle.

This creates a stable foundation for all later reference selection and annotation placement rules.