# Rebar AutoDim — Functional Requirements

## Purpose

This document defines the functional behavior required from Rebar AutoDim.

The requirements describe what the system must do without specifying implementation details.

---

## Functional Requirements

| ID | Requirement |
|---|---|
| FR-001 | The plugin must operate on the active Revit view. |
| FR-002 | The plugin must detect visible `Area Reinforcement` elements on the active view. |
| FR-003 | The plugin must process rectangular reinforcement zones. |
| FR-004 | The plugin must determine the visible boundaries of each supported zone. |
| FR-005 | The plugin must determine the overall width of each supported zone. |
| FR-006 | The plugin must determine the overall height of each supported zone. |
| FR-007 | The plugin must detect structural grids available on the active view. |
| FR-008 | The plugin must determine valid grid references for each applicable side of the zone. |
| FR-009 | The plugin must create an overall width dimension. |
| FR-010 | The plugin must create an overall height dimension. |
| FR-011 | The plugin must create grid offset dimensions where valid references exist. |
| FR-012 | The plugin must calculate dimension-line positions automatically. |
| FR-013 | The plugin must separate parallel dimension lines when multiple dimensions use the same side. |
| FR-014 | The plugin must create supporting detail geometry when required for dimension references. |
| FR-015 | The plugin must process all supported zones visible on the active view without requiring manual selection. |
| FR-016 | The plugin must skip unsupported or invalid zones instead of generating uncertain dimensions. |
| FR-017 | The plugin must preserve the original reinforcement geometry and structural model data. |
| FR-018 | The plugin must create native Revit annotation elements. |
| FR-019 | The plugin must support repeated execution on the same reinforcement zones. |
| FR-020 | The plugin must prevent accumulation of duplicate generated annotation sets after repeated execution. |
| FR-021 | The plugin must regenerate annotations based on the current model state after reinforcement geometry changes. |
| FR-022 | The plugin must report the processing result to the user. |

---

## Processing Behavior

For each supported reinforcement zone:

```text
Detect zone
    ↓
Read geometry
    ↓
Determine dimensions
    ↓
Find structural references
    ↓
Calculate placement
    ↓
Create annotations

If the zone cannot be processed safely:

Invalid / unsupported zone
        ↓
Skip zone
        ↓
Continue processing
Grid Reference Behavior

The plugin may create grid offset dimensions in four directions:

          Above
            ↑
            │
Left ← [ Area Reinforcement ] → Right
            │
            ↓
          Below

A dimension is required only when a valid reference exists in the corresponding direction.

Re-run Behavior

Repeated execution must not produce duplicate annotation sets.

Existing generated result
        ↓
Re-run command
        ↓
Replace previous result
        ↓
Create annotations from current geometry
Functional Boundary

The functional requirements do not include:

reinforcement calculation;
reinforcement creation;
non-rectangular zone processing;
multi-view batch processing;
automatic wall-based references;
full annotation collision solving.

Summary

The functional scope is centered on one operation:
Detect supported Area Reinforcement zones and generate a consistent dimension layout from existing Revit geometry.