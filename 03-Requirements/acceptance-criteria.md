# Rebar AutoDim — Acceptance Criteria

## Purpose

This document defines observable conditions used to verify that Rebar AutoDim behaves as expected.

---

## Acceptance Criteria

| ID | Acceptance Criterion |
|---|---|
| AC-001 | Given a supported active Revit view, when the plugin runs, then visible `Area Reinforcement` elements are detected for processing. |
| AC-002 | Given a non-rectangular reinforcement zone, when the plugin processes the view, then that zone is skipped. |
| AC-003 | Given a valid rectangular zone, when the plugin processes it, then one overall width dimension is created. |
| AC-004 | Given a valid rectangular zone, when the plugin processes it, then one overall height dimension is created. |
| AC-005 | Given a supported view orientation, when zone geometry is analyzed, then width and height are determined relative to the active view. |
| AC-006 | Given visible structural grids, when reference selection is performed, then grids are classified by orientation. |
| AC-007 | Given multiple grids around a zone, when the plugin selects a reference for one side, then only grids on that side are considered. |
| AC-008 | Given a valid nearest grid on a zone side, when the plugin creates annotations, then the corresponding grid offset dimension is created. |
| AC-009 | Given no valid grid on a zone side, when the plugin processes that side, then no grid dimension is created for that direction. |
| AC-010 | Given a grid coinciding with the zone boundary within tolerance, when the plugin processes that side, then no zero-length offset dimension is created. |
| AC-011 | Given multiple dimensions assigned to the same side, when annotations are placed, then separate offset levels are used. |
| AC-012 | Given multiple valid reinforcement zones on the active view, when the plugin runs, then all supported zones are processed without manual selection. |
| AC-013 | Given one invalid zone and other valid zones, when the plugin runs, then the invalid zone does not prevent processing of the valid zones where technically possible. |
| AC-014 | Given a successful execution, when the model is reviewed, then source reinforcement geometry and structural grid geometry remain unchanged. |
| AC-015 | Given generated output, when the elements are inspected in Revit, then annotations exist as native Revit elements. |
| AC-016 | Given annotations from a previous plugin execution, when the plugin runs again, then duplicate annotation sets are not accumulated. |
| AC-017 | Given reinforcement geometry changed after previous generation, when the plugin runs again, then annotations are regenerated from the current geometry. |
| AC-018 | Given the same unchanged model state and active view, when the plugin runs repeatedly, then the resulting annotation layout is equivalent. |
| AC-019 | Given an unsupported active view, when the plugin runs, then processing stops before model changes are created. |
| AC-020 | Given no supported reinforcement zones on the active view, when the plugin runs, then no annotations are created. |
| AC-021 | Given a failed write operation, when the corresponding transaction cannot complete, then changes from that failed transaction are rolled back. |
| AC-022 | Given command execution completes, when processing ends, then the user receives a processing result. |

---

## Core Success Scenario

```text
Given:
Supported Revit view
+
Visible rectangular Area Reinforcement
+
Valid nearby structural grids

When:
User runs Rebar AutoDim

Then:
Zone is detected
        ↓
Geometry is analyzed
        ↓
Overall dimensions are created
        ↓
Grid references are selected
        ↓
Grid dimensions are created
        ↓
Dimensions are positioned
        ↓
Native Revit annotations remain on the view

Missing Grid Scenario
Given:
Valid rectangular zone
+
No structural grid on one side

When:
Plugin processes the zone

Then:
Overall dimensions are created
+
Available grid dimensions are created
+
Missing-side grid dimension is omitted

The absence of one grid reference must not invalidate the complete zone.

Unsupported Geometry Scenario
Given:
Area Reinforcement
+
Geometry cannot be represented as a valid rectangle

When:
Plugin analyzes the zone

Then:
Zone is skipped
+
No approximate dimensions are created
Re-run Scenario
Given:
Zone already has plugin-generated annotations

When:
User runs Rebar AutoDim again

Then:
Previous generated result is replaced
        ↓
Current geometry is recalculated
        ↓
New annotation result is created

The command must not continuously accumulate duplicates.

Model Change Scenario
Initial geometry
      ↓
Generate annotations
      ↓
Reinforcement geometry changes
      ↓
Run plugin again
      ↓
Annotations reflect current geometry
Acceptance Principle

The generated result is accepted when it:

represents the current reinforcement geometry;
uses valid structural references;
follows defined placement rules;
does not modify source structural data;
remains repeatable after re-execution.
Summary

Acceptance is based on observable Revit behavior rather than internal implementation details.