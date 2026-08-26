# Rebar AutoDim — AS-IS Process

## Purpose

This document describes the manual workflow used to dimension `Area Reinforcement` zones before automation.

---

## Trigger

The process starts when a structural engineer prepares a Revit view for documentation and reinforcement zones already exist in the model.

---

## AS-IS Workflow

```text
Open structural view
        ↓
Find Area Reinforcement zone
        ↓
Identify zone boundaries
        ↓
Create supporting detail lines
        ↓
Create width dimension
        ↓
Create height dimension
        ↓
Find nearby structural grids
        ↓
Create grid offset dimensions
        ↓
Adjust dimension placement
        ↓
Review result
        ↓
Repeat for next zone

Manual Actions per Zone

For every supported reinforcement zone, the engineer performs the same set of operations:

identifies the visible reinforcement boundary;
recreates the zone outline where required for dimension references;
creates overall width and height dimensions;
selects nearby structural grids;
creates grid offset dimensions;
moves dimension lines to readable positions;
checks the final annotation result.
Main Problems
Problem	Impact
Repetitive operations	Increases drawing-production time
Manual reference selection	Risk of incorrect grid selection
Manual placement	Inconsistent annotation layout
Repeated edits after model changes	Additional maintenance work
Large number of zones	Higher probability of missed annotations
Model Change Scenario

When reinforcement geometry changes, the annotation result may no longer represent the current model state.

Reinforcement geometry changes
        ↓
Existing annotations become outdated
        ↓
Engineer reviews affected zones
        ↓
Dimensions are moved or recreated manually

The maintenance workflow therefore repeats part of the original dimensioning process.

Key Limitation

The AS-IS process relies on the engineer to manually interpret geometry that already exists in the Revit model.

There is no automated link between:

Area Reinforcement geometry
            and
required drawing dimensions
Summary

The manual process is predictable but repetitive.

The main automation opportunity is to replace geometry interpretation, reference selection and dimension placement with deterministic rules.