# Rebar AutoDim — Solution Overview

## Purpose

This document describes how Rebar AutoDim transforms existing `Area Reinforcement` geometry into native Revit dimensions.

The solution is designed as a deterministic processing pipeline executed inside Autodesk Revit 2025.

---

## High-Level Flow

```text
Active Revit View
        ↓
Detect Area Reinforcement
        ↓
Analyze Zone Geometry
        ↓
Resolve Structural References
        ↓
Calculate Dimension Placement
        ↓
Create Native Revit Annotations
        ↓
Store Generated Result

Main Responsibilities

The solution is responsible for:

detecting supported reinforcement zones;
interpreting geometry relative to the active view;
determining zone width and height;
selecting nearby structural grids;
calculating dimension placement;
creating native Revit dimensions;
preventing duplicate output after repeated execution.
Logical Components
Command Handler
      ↓
View Context
      ↓
Zone Detection
      ↓
Geometry Processing
      ↓
Grid Resolution
      ↓
Placement Logic
      ↓
Annotation Creation
      ↓
Generation Tracking

Each component has one main responsibility.

System Boundary

Rebar AutoDim runs inside Revit and uses the Revit API.

Input
active Revit document;
active supported view;
existing Area Reinforcement;
existing structural grids.
Output
overall width dimensions;
overall height dimensions;
grid offset dimensions;
supporting detail geometry;
generation metadata.
Key Design Decisions
View-Based Geometry

Geometry is interpreted relative to the active view instead of global project X/Y coordinates.

This keeps width, height and placement consistent across supported view orientations.

Directional Grid Selection

Grid references are selected independently for:

left;
right;
above;
below.

This prevents the system from choosing a geometrically closer grid on the wrong side.

Stable Dimension References

Supporting detail geometry is used when required to provide stable Revit references for dimensions.

Regeneration Instead of Patch

Repeated execution replaces the previous generated result.

Previous Result
      ↓
Delete
      ↓
Recalculate
      ↓
Create New Result

This is simpler and safer than updating individual dimensions.

Revit API Boundary

The plugin reads:

active view properties;
reinforcement geometry;
grid geometry;
existing generation metadata.

The plugin writes:

detail curves;
dimensions;
generated groups;
generation metadata.

The source reinforcement model remains unchanged.

Result

The solution converts existing model geometry into a consistent annotation layer without changing reinforcement design data.

Revit Model
    +
Rebar AutoDim
    ↓
Native Drawing Annotations

Summary
The architecture separates geometry analysis, reference selection, placement and annotation creation so that each part of the solution can evolve independently.