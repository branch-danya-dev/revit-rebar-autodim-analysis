# Revit Host Boundary

This area owns the explicit boundary between Rebar AutoDim system meaning and Autodesk Revit host/API behavior.

## Authority split

```text
Revit owns
→ document/view state
→ source element geometry
→ native Reference validity
→ native dimension validity
→ transaction semantics

Rebar AutoDim owns
→ interpretation for its supported annotation problem
→ semantic dimension targets
→ generated-result ownership
```

## Read boundary

The plugin consumes active-view context, `Area Reinforcement`, structural grids and existing plugin metadata.

## Write boundary

The plugin writes only its annotation layer:

- supporting detail curves;
- native dimensions;
- grouping/association elements where used;
- generation metadata;
- deletion of previous plugin-owned output.

Source reinforcement and structural-grid design data remain unchanged.

## Core translation

```text
semantic target
→ Revit-compatible native references
→ valid dimension line
→ native annotation creation
```

A host/API workaround may realize the intended meaning but must not redefine it.

## Canonical detail

→ [`transaction-and-failure-model.md`](transaction-and-failure-model.md) — reads/writes, one-zone transaction boundary, native rejection, rollback, fallback and verification.

## Failure scope

The system distinguishes:

```text
unsupported context / invalid geometry
→ fail before writes or skip zone

missing optional semantic target
→ not a failure

native realization failure
→ affected intended result cannot be silently reclassified

zone transaction failure
→ rollback that zone
```

The historical interaction model already used one transaction per supported zone, aligning technical atomicity with one independently meaningful annotation result.
