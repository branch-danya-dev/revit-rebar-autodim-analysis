# Rebar AutoDim — Components

## Purpose

This document describes the main logical components of Rebar AutoDim and the responsibility of each component.

---

## Component Model

```text
Revit Ribbon
     ↓
Command Handler
     ↓
View Context
     ↓
Zone Collector
     ↓
Geometry Processor
     ↓
Grid Resolver
     ↓
Placement Engine
     ↓
Annotation Generator
     ↓
Generation Tracker

Command Handler

Coordinates one plugin execution.

Responsibilities:

get active document and view;
validate execution context;
start processing;
manage transactions;
collect execution result.

It should not contain geometry or placement logic.

View Context

Represents the active Revit view as a local coordinate system.

Main data:

Origin
RightDirection
UpDirection
ViewDirection
view scale
crop information

It provides conversion between Revit XYZ coordinates and local view coordinates.

Model XYZ
   ↓
View Context
   ↓
(u, v, w)
Zone Collector

Finds Area Reinforcement elements available for processing.

Responsibilities:

collect elements visible on the active view;
exclude invalid elements;
return candidate zones.

Output:

List<AreaReinforcement>
Geometry Processor

Transforms raw reinforcement geometry into a normalized rectangular zone.

Responsibilities:

read boundary geometry;
project geometry into view coordinates;
determine left, right, top and bottom boundaries;
calculate width and height.

Logical output:

ZoneGeometry
├── Left
├── Right
├── Bottom
├── Top
├── Width
└── Height
Grid Resolver

Prepares and selects structural grid references.

Responsibilities:

collect visible grids;
classify them by orientation;
remove equivalent positions;
find the nearest valid grid for each direction.

Output:

GridReferences
├── Left?
├── Right?
├── Above?
└── Below?
Placement Engine

Determines where dimension lines should be placed.

Responsibilities:

select the preferred side;
calculate base offset;
calculate additional offset levels;
prevent parallel dimensions from occupying the same position.

Example:

             Top Grid Dimension

        ┌──────────────────┐
Left    │ Area Reinforcement│    Right
Grid    └──────────────────┘    Dimensions

      Width / Bottom Grid Dimensions
Annotation Generator

Creates the final Revit elements.

Responsibilities:

create supporting detail curves;
build dimension references;
create overall dimensions;
create grid offset dimensions;
return identifiers of generated elements.

The component receives calculated geometry and placement decisions instead of calculating them itself.

Generation Tracker

Controls repeated execution.

Responsibilities:

identify previous plugin-generated output;
remove outdated generated elements;
associate the new result with the source reinforcement zone.
Area Reinforcement
        │
        ├── Generated Group
        │
        └── Generation Metadata
Component Responsibilities
Component	Main Question
Command Handler	What should be executed?
View Context	How is geometry oriented on this view?
Zone Collector	What should be processed?
Geometry Processor	What is the zone geometry?
Grid Resolver	Which grids should be referenced?
Placement Engine	Where should dimensions be placed?
Annotation Generator	How is the result created in Revit?
Generation Tracker	How is the result regenerated safely?
Summary

The solution separates geometry, reference selection, placement and Revit element creation.

This keeps the processing pipeline understandable and allows individual rules to change independently.