# Revit Host Boundary

This area owns the explicit boundary between Rebar AutoDim system meaning and Autodesk Revit host/API behavior.

It answers:

> **Which Revit facts and capabilities does the plugin consume, what may it write, and what host constraints must the system respect?**

## Revit as external authority

Revit is authoritative for:

- document and view state;
- source `Area Reinforcement` elements and their geometry;
- structural grids and their geometry/references;
- validity rules for Revit elements and references;
- transaction semantics;
- native dimension creation behavior.

Rebar AutoDim is authoritative for:

- how those facts are interpreted for its supported annotation problem;
- which dimension intent should be produced;
- ownership of its own generated annotation result.

```text
Revit evidence / capabilities
!=
Rebar AutoDim analytical decisions
```

## Read boundary

The plugin may read:

- active document/view properties;
- view scale/orientation/crop context;
- visible `Area Reinforcement` identity and geometry;
- visible structural grid geometry and references;
- plugin-generated metadata and previous result identity.

Reading a Revit fact does not transfer ownership of that fact to the plugin.

## Write boundary

The plugin writes only to its annotation layer:

```text
Detail Curves
Dimensions
Generated Groups / owned annotation elements
Generation Metadata
```

It must not modify source reinforcement geometry as part of dimension generation.

## Dimension API constraint

A semantic dimension intent requires Revit-compatible geometric references and a valid dimension line.

```text
semantic targets
+
Revit-compatible References
+
valid line in active annotation plane
        ↓
NewDimension / native dimension creation
```

If Revit rejects the representation, the system must not reinterpret the structural target merely to force a successful API call.

## Supporting reference realization

When the raw reinforcement boundary cannot provide stable dimension references, the plugin may create supporting detail curves.

This is a host-boundary adaptation:

```text
canonical zone geometry
        ↓
Revit API limitation
        ↓
supporting detail geometry
        ↓
usable Revit References
```

The workaround realizes existing system meaning; it does not own that meaning.

## Transaction boundary

All writes occur inside Revit transactions.

At the system level, the requirement is atomic safety:

```text
prepare current result
        ↓
transaction
├── remove old plugin-owned output
├── create supporting geometry
├── create dimensions
├── persist ownership metadata
└── commit
```

When safe completion is impossible:

```text
unsafe write failure
        ↓
rollback affected transaction
        ↓
source structural model remains unchanged
```

## Failure locality

There are two different failure scopes:

```text
local annotation/reference failure
→ omit/skip affected output where system rules allow
→ continue other valid work

transaction/document safety failure
→ rollback affected write scope
```

These must not be collapsed into one generic exception strategy.

## Host boundary does not own workflow rules

Revit API methods, transactions and element classes are implementation evidence.

Rules such as directional grid selection, required width/height dimensions, regeneration semantics or skip-uncertain behavior belong to their canonical system owners even if their implementation is expressed through Revit API calls.
