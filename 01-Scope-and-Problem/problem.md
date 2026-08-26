# Rebar AutoDim — Problem

## Problem Statement

Structural reinforcement documentation in Revit requires dimensions that are not generated automatically for `Area Reinforcement` zones.

As a result, engineers have to reproduce model geometry manually as drawing annotations.

---

## Manual Workflow

For each reinforcement zone, the engineer typically:

- identifies the reinforcement boundary;
- creates supporting detail lines;
- creates width and height dimensions;
- finds nearby structural grids;
- creates offset dimensions to those grids;
- adjusts dimension-line positions;
- repeats the same process after geometry changes.

For a view containing many reinforcement zones, the same sequence is repeated multiple times.

---

## Main Pain Points

### Repetition

The workflow consists mostly of predictable actions that do not require new engineering decisions.

### Time Cost

Dimensioning every zone manually increases drawing-production time.

### Error Risk

Typical problems include:

- missed reinforcement zones;
- missing dimensions;
- incorrect structural references;
- outdated annotations after model changes;
- overlapping dimension lines.

### Maintenance

When reinforcement geometry changes, the annotation set must be reviewed and corrected again.

---

## Root Cause

The required geometry already exists in the Revit model.

The missing part is the transformation from model geometry to drawing annotations.

```text
AS-IS

Area Reinforcement
        ↓
Manual geometry interpretation
        ↓
Manual reference selection
        ↓
Manual dimension placement
        ↓
Drawing annotations

The problem is therefore not the absence of geometric data, but the absence of a deterministic annotation workflow.

Automation Opportunity

The workflow is suitable for automation because:

the target element type is known;
supported zones are rectangular;
width and height can be derived from geometry;
structural grids can be classified by direction;
nearest references can be selected using defined rules;
annotation positions can be calculated using repeatable offsets.
Target

Replace repeated manual dimensioning with one deterministic command:

Area Reinforcement
        ↓
Geometry analysis
        ↓
Reference selection
        ↓
Dimension placement
        ↓
Native Revit annotations

The engineer remains responsible for reviewing the resulting drawing, while the plugin performs the repetitive annotation work.