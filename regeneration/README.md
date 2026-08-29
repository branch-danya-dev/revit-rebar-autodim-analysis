# Regeneration and Generated-Output Ownership

This area owns the lifecycle of plugin-generated output across repeated command execution.

It answers:

> **Which annotation result belongs to this source zone, and how is that result replaced safely when the model or view changes?**

## Owned knowledge

Regeneration owns:

- association between source `Area Reinforcement` and plugin-generated result;
- persistent generation metadata needed to locate previous output;
- full-replacement strategy;
- behavior when previous output is missing or partially edited;
- repeated-execution equivalence;
- stale-result removal before creating the new committed result.

It does **not** own:

- what dimensions belong in the annotation set — [`annotations/`](../annotations/);
- how source geometry is interpreted — [`geometry/`](../geometry/);
- transaction rollback mechanics — [`revit-boundary/`](../revit-boundary/).

## Ownership model

```text
Area Reinforcement
        ↓ source identity
Generation Metadata
        ↓ identifies
Current Generated Annotation Result
```

The source reinforcement owns the structural design. Rebar AutoDim owns only the annotation result that it generated for that source.

## Full regeneration strategy

Repeated execution uses replacement rather than incremental synchronization.

```text
find previous plugin-owned result
        ↓
delete previous result
        ↓
read current source/context
        ↓
recalculate complete result
        ↓
create new annotation set
        ↓
store updated ownership metadata
```

This policy is a system decision, not merely an implementation convenience.

## Idempotency meaning

For an unchanged source and execution context:

```text
Run 1 → Result A
Run 2 → replace A → Result A'
```

`A'` should be semantically equivalent to `A`.

Idempotency therefore means:

> **Repeated execution converges to one equivalent current result rather than accumulating output.**

It does not require preserving Revit element IDs between runs.

## Model-change behavior

When source geometry or relevant view context changes, the old annotation set becomes stale evidence.

```text
source/context changed
        ↓
previous generated result no longer canonical
        ↓
next run recalculates from current Revit state
```

The plugin does not attempt to infer which individual dimension is still reusable.

## Missing previous result

A user may manually delete the generated group while metadata remains.

```text
metadata exists
+
referenced result missing
        ↓
treat previous result as absent
        ↓
generate current result
        ↓
repair metadata association
```

This is recoverable and must not block processing.

## Partial manual modification

If users partially modify or delete plugin-owned output, the next run does not merge manual edits with calculated state.

```text
partial generated result
        ↓
remove remaining plugin-owned output
        ↓
regenerate complete result
```

This boundary prevents ambiguous ownership between manual edits and automation.

## Atomic replacement requirement

The desired system outcome is never:

```text
old result partially deleted
+
new result partially created
```

Transaction design must protect regeneration from leaving such a mixed state. The technical realization belongs to [`revit-boundary/`](../revit-boundary/).
