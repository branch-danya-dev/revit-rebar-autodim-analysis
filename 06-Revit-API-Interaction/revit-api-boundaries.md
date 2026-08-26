# Rebar AutoDim — Revit API Boundaries

## Purpose

This document describes how Rebar AutoDim interacts with Autodesk Revit through the Revit API.

The goal is to separate model reading, annotation creation and persistence responsibilities.

---

## API Interaction Scope

The plugin interacts with four main Revit areas:

- active document and view;
- reinforcement geometry;
- structural grids;
- annotation and metadata creation.

---

## Read Operations

The plugin reads existing model and view data.

### Active View

Used to obtain:

- view origin;
- right direction;
- up direction;
- view direction;
- scale;
- crop information.

### Area Reinforcement

Used to obtain:

- visible reinforcement zones;
- boundary geometry;
- element identity.

### Structural Grids

Used to obtain:

- visible grids;
- grid geometry;
- valid dimension references.

### Existing Generated Metadata

Used to identify previously generated annotation results.

---

## Write Operations

The plugin writes only annotation-related data.

Generated elements may include:

- detail curves;
- dimensions;
- groups;
- persistent generation metadata.

The source reinforcement model is not modified.

---

## Element Collection

Processing is scoped to the active view.

Conceptually:

```text
Active View
    ↓
Collect Area Reinforcement
    ↓
Collect Structural Grids

View-scoped collection avoids processing unrelated elements from the entire Revit document.

Geometry Access

The preferred geometry source is the reinforcement boundary.

Area Reinforcement
        ↓
Boundary Curve IDs
        ↓
Model Curves
        ↓
Curve Geometry

If boundary geometry cannot be used, view-specific bounding geometry may be used as a fallback.

Dimension References

Revit dimensions require valid geometric references.

Raw Area Reinforcement geometry may not always provide references that NewDimension can use reliably.

The plugin therefore may create supporting detail curves:

Area Reinforcement Geometry
        ↓
Normalized Rectangle
        ↓
Detail Curves
        ↓
Stable References
        ↓
ReferenceArray
        ↓
Revit Dimension

Structural grid references are used directly where valid.

Dimension Creation

Native dimensions are created through the Revit document API.

Conceptually:

Reference 1
    +
Reference 2
    +
Dimension Line
    ↓
NewDimension
    ↓
Native Revit Dimension

The dimension line must lie in the active annotation plane and use compatible references.

Transactions

All write operations are executed inside Revit transactions.

Start Transaction
       ↓
Delete old generated result
       ↓
Create detail geometry
       ↓
Create dimensions
       ↓
Store metadata
       ↓
Commit

If the operation cannot complete safely, the affected transaction must be rolled back.

Generated Result Tracking

Each generated annotation set is associated with its source Area Reinforcement.

The relationship is conceptually:

Area Reinforcement
        ↓
Generation Metadata
        ↓
Generated Annotation Group

Persistent metadata allows the plugin to identify the previous result during re-execution.

Re-run Interaction

On repeated execution:

Read source element
        ↓
Read stored generation metadata
        ↓
Find previous generated result
        ↓
Delete previous result
        ↓
Create new annotations
        ↓
Store updated metadata

The API interaction follows regeneration rather than incremental synchronization.

API Boundary
Revit Data	Access
Active view	Read
Area Reinforcement geometry	Read
Structural grids	Read
Structural model geometry	Read only
Detail curves	Create / Delete
Dimensions	Create / Delete
Generated groups	Create / Delete
Generation metadata	Read / Write
Summary

Rebar AutoDim treats Revit as both:

the source of model geometry and structural references;
the host for generated native annotations.

The plugin reads structural data but limits write operations to its own generated annotation layer.