# Rebar AutoDim — Non-Functional Requirements

## Purpose

This document defines quality attributes and technical constraints that the solution must satisfy.

---

## Non-Functional Requirements

| ID | Requirement |
|---|---|
| NFR-001 | The plugin must be compatible with Autodesk Revit 2025. |
| NFR-002 | The plugin must preserve the integrity of the source reinforcement model. |
| NFR-003 | Geometry calculations must be based on the active view coordinate system. |
| NFR-004 | Repeated execution on an unchanged model must produce an equivalent annotation result. |
| NFR-005 | Generated dimensions must follow consistent placement rules. |
| NFR-006 | Failure of one reinforcement zone should not prevent processing of other valid zones where technically possible. |
| NFR-007 | All document modifications must be performed through valid Revit transactions. |
| NFR-008 | Failed transactions must not leave the Revit document in an invalid or partially committed state. |
| NFR-009 | Generated annotations must remain readable under supported scenarios. |
| NFR-010 | The plugin should process all supported zones on a typical structural view within a reasonable interactive execution time. |
| NFR-011 | Generated annotation elements must be identifiable for safe regeneration. |
| NFR-012 | Geometry processing, reference selection and dimension placement logic should remain logically separated. |

---

## Deterministic Behavior

The same input should produce the same logical result.

```text
Same model state
      +
Same active view
      ↓
Same geometry interpretation
      ↓
Same reference selection
      ↓
Equivalent annotation layout

This is especially important for repeated execution and model updates.

Model Safety

The plugin must not modify:

Area Reinforcement geometry;
reinforcement design parameters;
structural grid geometry;
host structural elements.

Only plugin-generated annotation elements may be created, replaced or removed.

Transaction Safety

All write operations must respect Revit transaction requirements.

Start transaction
      ↓
Create / replace annotations
      ↓
Successful?
   /        \
 Yes        No
  ↓          ↓
Commit    Rollback

A failed operation must not leave an open or invalid transaction state.

View Independence

The solution must not assume that project X/Y coordinates always match horizontal and vertical directions on screen.

Geometry must be interpreted relative to:

view origin;
view right direction;
view up direction;
view normal.

This allows the same processing logic to work consistently across supported view orientations.

Reliability

A local processing failure should remain local where possible.

Example:

Zone A → Success
Zone B → Invalid geometry → Skip
Zone C → Success

Failure of Zone B should not invalidate results already created for Zones A and C.

Performance

The plugin is intended for interactive use inside Revit.

The user should be able to process multiple reinforcement zones with one command without manually selecting or processing each zone separately.

No strict SLA is defined for the current version.

Maintainability

The solution should keep the following concerns separated:

Geometry Processing
        ↓
Reference Selection
        ↓
Dimension Placement
        ↓
Revit Annotation Creation

This separation allows individual rules to be changed without redesigning the entire processing pipeline.

Summary

The main quality goals are:

deterministic output;
model safety;
transaction safety;
view-independent geometry processing;
isolated failures;
maintainable processing logic.