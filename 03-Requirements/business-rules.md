# Rebar AutoDim — Business Rules

## Purpose

This document defines the rules that govern geometry interpretation, reference selection and annotation behavior.

These rules describe how the system should make decisions during processing.

---

## Business Rules

| ID | Rule |
|---|---|
| BR-001 | Only `Area Reinforcement` elements visible on the active view are processed. |
| BR-002 | Only rectangular reinforcement zones are supported. |
| BR-003 | Width and height are determined relative to the active view, not global project X/Y coordinates. |
| BR-004 | Each valid zone must receive one overall width dimension and one overall height dimension. |
| BR-005 | Structural grids must be classified by orientation before reference selection. |
| BR-006 | Grid references are selected independently for left, right, above and below directions. |
| BR-007 | A grid located on the opposite side of a zone must not be selected for that direction, even if it is geometrically closer. |
| BR-008 | If no valid grid exists on a side, no grid offset dimension is created for that side. |
| BR-009 | A grid coinciding with the zone boundary within tolerance does not require a zero-length offset dimension. |
| BR-010 | Dimension placement follows a deterministic side policy. |
| BR-011 | Parallel dimensions placed on the same side must use separate offset levels. |
| BR-012 | Supporting detail geometry may be created when required to provide stable Revit dimension references. |
| BR-013 | The source reinforcement geometry must remain unchanged. |
| BR-014 | Repeated execution must replace the previous generated annotation result instead of accumulating duplicates. |
| BR-015 | Invalid or unsupported zones must be skipped rather than dimensioned approximately. |
| BR-016 | Processing failure of one zone should remain isolated where technically possible. |

---

## Grid Selection Rules

Grid selection is directional.

For a reinforcement zone:

```text
          nearest above grid
                 ↑
                 │

nearest left ← [ ZONE ] → nearest right

                 │
                 ↓
          nearest below grid

          The plugin searches each direction independently.

Example:

Zone left boundary = X

Candidate grids:
Grid A → left of zone, distance 1200
Grid B → right of zone, distance 300

For the left-side dimension:

Grid A → valid
Grid B → ignored

The nearest grid is therefore the nearest valid grid on the required side, not the globally nearest grid.

Overall Dimension Placement

Default placement:

Dimension	Side
Overall width	Bottom
Overall height	Right

These positions provide a predictable base layout.

Grid Dimension Placement

Default placement:

Grid reference	Dimension side
Left grid	Left
Right grid	Right
Above grid	Top
Below grid	Bottom

If multiple dimensions occupy the same side, additional offset levels are applied.

Parallel Dimension Rule

Dimensions must not be placed directly on top of each other.

Zone boundary
      │
      ├── Offset level 1
      │
      ├── Offset level 2
      │
      └── Offset level 3

The required level depends on the dimensions already assigned to that side.

Missing Reference

The absence of a structural grid does not invalidate the zone.

Example:

Left   → grid found
Right  → no grid
Above  → grid found
Below  → no grid

Expected result:

Overall width
Overall height
Left grid dimension
Above grid dimension

No artificial reference should be created to compensate for a missing grid.

Regeneration Rule

Plugin-generated annotations are treated as one regenerable result associated with the reinforcement zone.

Previous annotation result
          ↓
Delete
          ↓
Read current model state
          ↓
Recalculate
          ↓
Create new annotation result

The plugin does not attempt to patch individual outdated dimensions.

Safety Rule

If the system cannot determine a valid geometric or Revit reference, it must skip the affected annotation.

Uncertain geometry
        ↓
Do not create dimension

A missing dimension is preferable to an incorrect dimension that misrepresents the structural model.

Summary

The core decision rules are:

interpret geometry in active-view coordinates;
select references by direction;
place dimensions deterministically;
skip uncertain cases;
regenerate instead of duplicating.