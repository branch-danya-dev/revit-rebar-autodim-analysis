# Rebar AutoDim — Grid Selection

## Purpose

This document describes how Rebar AutoDim finds structural grids that can be used as external references for reinforcement-zone dimensions.

The selection is directional and based on the active-view coordinate system.

---

## Input

The grid-selection stage receives:

- active Revit view;
- normalized `ZoneGeometry`;
- visible structural grids.

---

## Grid Preparation

Visible grids are collected once for the current command execution.

Each grid is projected into the local view coordinate system and classified by orientation.

```text
Vertical grid   → constant u
Horizontal grid → constant v

Equivalent grid positions may be deduplicated before selection.

Directional Search

Grid references are selected independently for four directions:

          Above
            ↑
            │

Left ← [ Zone ] → Right

            │
            ↓
          Below

The search is not based on global minimum distance.

A grid is valid only when it is located on the required side of the zone.

Selection Rules

For a normalized zone:

Left   = L
Right  = R
Bottom = B
Top    = T

The nearest valid grids are:

Left   → nearest vertical grid where grid.u < L
Right  → nearest vertical grid where grid.u > R
Below  → nearest horizontal grid where grid.v < B
Above  → nearest horizontal grid where grid.v > T
Example
Vertical grids:

G1      G2        [ Zone ]      G3
│       │                        │

If:

distance(G1, Zone) = 3000
distance(G2, Zone) = 1200
distance(G3, Zone) = 400

Then:

Left reference  → G2
Right reference → G3

G3 cannot be selected for the left side even though it is the closest grid overall.

Missing Grid

A valid grid may not exist on every side.

Example:

Left   → found
Right  → found
Above  → not found
Below  → found

The result is:

Left grid dimension
Right grid dimension
Below grid dimension
No above-grid dimension

Missing one directional reference does not invalidate the reinforcement zone.

Coincident Grid

If a grid coincides with the corresponding zone boundary within geometric tolerance, an offset dimension is not required.

Grid position ≈ Zone boundary
        ↓
No zero-length dimension
Output

The selection stage produces:

GridReferences
├── LeftGrid?
├── RightGrid?
├── AboveGrid?
└── BelowGrid?

Each reference is optional.

The result is passed to the dimension-placement stage.

Summary

Grid selection follows three rules:

classify grids relative to the active view;
search each direction independently;
use the nearest valid grid only on that side.