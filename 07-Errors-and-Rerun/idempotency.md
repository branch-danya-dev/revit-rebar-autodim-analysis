# Rebar AutoDim — Idempotency and Re-run

## Purpose

This document describes how Rebar AutoDim behaves when the command is executed multiple times on the same reinforcement zone.

The goal is to prevent duplicate annotations and keep the generated result aligned with the current model state.

---

## Problem

Without generation tracking, repeated execution would create another set of dimensions every time the user runs the plugin.

```text
Run 1 → Annotation Set A
Run 2 → Annotation Set A + B
Run 3 → Annotation Set A + B + C

This creates duplicate dimensions and makes the drawing unreliable.

Regeneration Strategy

Rebar AutoDim uses full regeneration instead of incremental updates.

Existing generated result
        ↓
Delete
        ↓
Read current geometry
        ↓
Recalculate
        ↓
Create new result

The plugin does not try to patch individual dimensions.

Generated Result Tracking

Each processed Area Reinforcement zone is associated with its generated annotation result.

Conceptually:

Area Reinforcement
        ↓
Generation Metadata
        ↓
Generated Annotation Group

The metadata stores enough information to locate the previous generated result during the next execution.

Re-run Flow
Run command
    ↓
Read source Area Reinforcement
    ↓
Check generation metadata
    ↓
Previous result exists?
   /                  \
 Yes                  No
  ↓                    ↓
Delete old result      Continue
   \                  /
        ↓
Read current geometry
        ↓
Generate annotations
        ↓
Create new group
        ↓
Store updated metadata
Model Change Scenario

If the reinforcement geometry changes after the first execution:

Initial zone
      ↓
Generate annotations
      ↓
Zone geometry changes
      ↓
Run plugin again
      ↓
Old result removed
      ↓
New geometry analyzed
      ↓
Updated annotations created

The generated result therefore reflects the current model state.

Missing Previous Result

The user may manually delete the generated annotation group.

In this case:

Metadata exists
      ↓
Referenced group not found
      ↓
Treat previous result as missing
      ↓
Generate new result
      ↓
Update metadata

The missing group does not block processing.

Partial Manual Changes

If only part of the generated output is manually deleted, Rebar AutoDim does not attempt to preserve or repair the remaining elements.

The next execution replaces the complete generated result.

Partially modified result
        ↓
Remove remaining generated output
        ↓
Regenerate complete annotation set

This keeps synchronization logic simple and predictable.

Idempotency Rule

For an unchanged model state:

Run 1
  ↓
Annotation Result A

Run 2
  ↓
Replace A
  ↓
Equivalent Annotation Result A'

Repeated execution should produce an equivalent result rather than additional output.

Why Full Regeneration

Full regeneration was chosen because it avoids complex synchronization logic.

Advantages:

no duplicate accumulation;
no need to compare individual dimensions;
easier handling of model changes;
simpler recovery after manual edits;
deterministic output.
Failure Behavior

If regeneration fails before the new result can be committed, transaction handling must prevent the document from being left in an invalid state.

The source Area Reinforcement remains unchanged.

Summary

The re-run strategy is based on three rules:

identify plugin-generated output;
replace rather than append;
always regenerate from the current model state.