# Rebar AutoDim — System Analysis Portfolio

> Automated dimensioning of rectangular `Area Reinforcement` zones in Autodesk Revit 2025.

Rebar AutoDim is a custom Revit plugin created to replace repetitive manual reinforcement annotation work with deterministic geometry-based automation.

The project covers the full path from problem analysis and requirements to system design, Revit API interaction and production-ready behavior.

---

## Problem

Before automation, engineers manually processed each reinforcement zone:

- recreated supporting detail geometry;
- added overall width and height dimensions;
- selected nearby structural grids;
- created grid offset dimensions;
- adjusted parallel dimension lines;
- repeated the same work after model changes.

The required geometry already existed in the Revit model, but there was no automatic workflow that converted it into a complete annotation layout.

---

## Solution

Rebar AutoDim processes all supported `Area Reinforcement` zones visible on the active view.

```text
Area Reinforcement
        ↓
Geometry Analysis
        ↓
Grid Selection
        ↓
Dimension Placement
        ↓
Native Revit Annotations
```

The solution:

- interprets geometry in active-view coordinates;
- calculates zone width and height;
- finds the nearest valid structural grids by direction;
- creates deterministic dimension layouts;
- uses stable Revit references for dimension creation;
- regenerates previous output instead of accumulating duplicates.

---

## Before / After

| Before | After |
|---|---|
| Manual zone-by-zone processing | One command per active view |
| Manual grid selection | Directional rule-based selection |
| Manual dimension placement | Deterministic placement |
| Manual correction after changes | Full regeneration |

### Before

![Before Rebar AutoDim](09-Result/before.jpg)

### After

![After Rebar AutoDim](09-Result/after.jpg)

---

## Key System Decisions

### View-based geometry

Width, height and placement are calculated relative to the active Revit view instead of global project X/Y axes.

### Directional grid selection

The nearest structural reference is selected independently for `left`, `right`, `above` and `below`.

### Stable dimension references

Supporting detail geometry is created where required to provide references accepted by the Revit dimension API.

### Regeneration instead of patching

```text
Previous Result
      ↓
Delete
      ↓
Read Current Geometry
      ↓
Recalculate
      ↓
Create New Result
```

This keeps repeated execution predictable and prevents duplicate annotations.

---

## Documentation

| Section | Content |
|---|---|
| [01 — Scope and Problem](01-Scope-and-Problem/) | Scope, constraints and problem definition |
| [02 — AS-IS and TO-BE](02-AS-IS-and-TO-BE/) | Manual and automated workflows |
| [03 — Requirements](03-Requirements/) | Functional requirements, NFRs, business rules and acceptance criteria |
| [04 — System Design](04-System-Design/) | Solution overview, components and system context |
| [05 — Geometry and Placement](05-Geometry-and-Placement/) | Geometry normalization, grid selection and dimension placement |
| [06 — Revit API Interaction](06-Revit-API-Interaction/) | API boundaries and interaction sequence |
| [07 — Errors and Re-run](07-Errors-and-Rerun/) | Edge cases, failure behavior and idempotency |
| [08 — Traceability](08-Traceability/) | Requirement-to-behavior mapping |
| [09 — Result](09-Result/) | Before / after and project outcome |

---

## Project Outcome

The solution was accepted by the customer and introduced into regular company use.

It replaced a repetitive manual documentation workflow with a deterministic Revit API-based process.

---

## My Contribution

I designed and implemented the solution from scratch, including:

- requirements clarification;
- AS-IS / TO-BE analysis;
- automation rules;
- geometry-processing logic;
- grid-selection rules;
- dimension-placement logic;
- Revit API interaction design;
- repeated-execution behavior;
- implementation and testing.

---

## Tech Context

`Autodesk Revit 2025` · `Revit API` · `C#` · `.NET` · `PlantUML`

---

## Implementation

This repository focuses on system analysis and solution design.

The source-code repository can be linked here separately when published.
