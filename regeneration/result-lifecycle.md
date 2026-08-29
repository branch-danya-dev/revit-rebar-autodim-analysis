# Rebar AutoDim — Generated Result Lifecycle

This document owns the lifecycle of plugin-generated output across repeated command execution.

## Ownership relation

The system must be able to resolve:

```text
Source Area Reinforcement
→ current plugin-owned annotation result
```

The physical implementation may use a group, element identifiers and/or persistent metadata. Those are representations of this ownership relation.

## One-current-result rule

For one source zone and one current model/view state, the plugin owns one current annotation result.

```text
Run 1
→ Result A

Run 2
→ replace A
→ Result A'
```

Repeated execution does not append another independent result.

## Full regeneration

Rebar AutoDim intentionally regenerates rather than patches individual dimensions.

```text
read previous ownership
→ locate old generated result
→ read current source evidence
→ recalculate complete intended result
→ replace old output transactionally
→ store new ownership metadata
```

The regenerated result is based on current model state, not on previous dimension positions.

## Why not incremental synchronization

Full regeneration avoids having to decide whether each individual generated dimension is stale, manually edited or still compatible with changed source geometry.

It provides simpler convergence semantics:

> **The current generated result is a function of the current supported source state, not a chain of patches.**

## Geometry change

```text
source geometry changes
→ previous result becomes stale
→ user runs command
→ current geometry re-evaluated
→ new result replaces old result
```

No automatic live synchronization is implied. Re-execution is the explicit refresh trigger.

## Previous output manually deleted

Metadata may survive after the generated group/elements were removed manually.

```text
metadata exists
+
referenced result absent
→ treat old result as already missing
→ generate current result
→ repair ownership metadata
```

Missing previous output must not block regeneration.

## Partial manual modification

If only some generated elements remain, the system does not attempt to preserve the manually modified subset.

```text
partial old result
→ identify remaining plugin-owned output
→ replace whole generated result
```

This preserves one-result semantics and avoids mixed-generation state.

## Equivalent-input rule

Given unchanged source evidence and the same active view:

```text
Run N
→ logically equivalent annotation content and placement
```

Native Revit element IDs may differ after regeneration. Identity equivalence is therefore semantic, not necessarily object-ID equality.

## Failure during replacement

Replacement is not successful until the new zone result is committed safely.

A failed transaction must not be described as a successful regeneration simply because old elements were deleted earlier inside that transaction.

The transaction rollback must preserve document consistency.

Detailed transaction authority lives in [`../revit-boundary/transaction-and-failure-model.md`](../revit-boundary/transaction-and-failure-model.md).

## Legacy anchors

This owner absorbs:

- `BR-014`;
- `FR-019`, `FR-020`, `FR-021`;
- `NFR-004`, `NFR-011`;
- `AC-016`, `AC-017`, `AC-018`.
