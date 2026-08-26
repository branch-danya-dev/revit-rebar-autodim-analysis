# Rebar AutoDim — TO-BE Process

## Purpose

This document describes the target workflow after introducing Rebar AutoDim.

The goal is to replace repeated manual dimensioning with one deterministic command.

---

## Trigger

The process starts when the user opens a supported structural Revit view containing existing `Area Reinforcement` zones.

---

## TO-BE Workflow

```text
Open structural view
        ↓
Run Rebar AutoDim
        ↓
Detect visible Area Reinforcement zones
        ↓
Analyze zone geometry
        ↓
Determine width and height
        ↓
Find valid structural grids
        ↓
Calculate dimension placement
        ↓
Create native Revit annotations
        ↓
Review result

Automated Actions

For each supported reinforcement zone, the plugin:

detects the element on the active view;
extracts and normalizes its geometry;
determines the zone boundaries;
calculates overall width and height;
selects valid nearby structural grids;
calculates dimension-line positions;
creates required dimensions and supporting detail geometry.

No manual zone selection is required in the default workflow.

User Actions

The user is responsible for:

opening the required structural view;
running the plugin command;
reviewing the generated annotation result.

The plugin does not replace engineering review.

Processing Scope

One command processes all supported reinforcement zones visible on the active view.

1 active view
     ↓
N reinforcement zones
     ↓
Automatic processing
     ↓
N annotation results

Zones that cannot be processed safely are skipped rather than dimensioned approximately.

Model Change Scenario

When reinforcement geometry changes, the plugin can be executed again.

Reinforcement geometry changes
        ↓
Run Rebar AutoDim again
        ↓
Remove previous generated result
        ↓
Recalculate current geometry
        ↓
Create updated annotations

This avoids manual synchronization of individual dimensions.

Expected Improvement
AS-IS	TO-BE
Manual zone processing	Automatic zone detection
Manual geometry interpretation	Geometry derived from the model
Manual grid selection	Rule-based reference selection
Manual dimension placement	Deterministic placement
Manual correction after changes	Regeneration
Multiple actions per zone	One command per view
Result

The TO-BE process transforms reinforcement geometry directly into a consistent drawing annotation layer.

Model geometry
      ↓
Rebar AutoDim
      ↓
Native Revit annotations

The engineer keeps control of the final drawing while repetitive annotation work is automated.