# Rebar AutoDim — Scope

## Purpose

Rebar AutoDim is a custom Autodesk Revit 2025 plugin developed to automate dimensioning of rectangular `Area Reinforcement` zones on structural views.

The solution was developed from scratch for a construction company to reduce repetitive reinforcement-documentation work.

---

## In Scope

The plugin covers:

- Autodesk Revit 2025;
- active Revit view;
- visible `Area Reinforcement` elements;
- rectangular reinforcement zones;
- zone width and height dimensions;
- dimensions to nearby structural grids;
- supporting detail geometry;
- deterministic dimension placement;
- repeated execution without duplicate accumulation.

---

## User

Primary users are structural-design specialists working with reinforcement documentation in Autodesk Revit.

The typical interaction is:

```text
Open structural view
        ↓
Run Rebar AutoDim
        ↓
Review generated annotations

System Boundary

The plugin:

reads existing reinforcement geometry;
analyzes the zone in active-view coordinates;
selects structural references;
creates native Revit annotations.

The plugin does not modify reinforcement design data.

Out of Scope

The current implementation does not include:

reinforcement calculations;
creation of reinforcement zones;
non-rectangular geometry;
batch processing of multiple views or sheets;
wall-based references in the core workflow;
full annotation collision solving.
Platform
Parameter	Value
Host	Autodesk Revit
Version	Revit 2025
Target element	Area Reinforcement
Geometry	Rectangular
Processing scope	Active view
Output	Native Revit annotations
Integration	Revit API

Summary

The solution focuses on one specific workflow:
Convert existing rectangular Area Reinforcement geometry into a consistent dimension layout on the active Revit view.