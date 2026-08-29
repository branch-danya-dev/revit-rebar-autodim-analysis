# Regeneration and Generated-Output Ownership

This area owns **which generated annotation result belongs to a source zone and how that result evolves across repeated command execution**.

## Ownership model

```text
Area Reinforcement source identity
        ↓
Generation ownership metadata
        ↓
One current plugin-owned annotation result
```

## Core strategy

```text
find previous result
→ replace whole result
→ reread current Revit evidence
→ recalculate
→ commit one new current result
```

Rebar AutoDim deliberately regenerates instead of incrementally patching individual dimensions.

## Idempotency meaning

For unchanged source evidence and the same view context, repeated execution converges to a logically equivalent result. Native element IDs do not need to remain identical.

## Canonical detail

→ [`result-lifecycle.md`](result-lifecycle.md) — one-current-result rule, full regeneration, manual deletion/partial edit recovery, equivalence and failure semantics.

## Does not own

- what dimensions constitute a result → [`../annotations/`](../annotations/);
- source geometry → [`../geometry/`](../geometry/);
- rollback mechanics → [`../revit-boundary/`](../revit-boundary/).

The desired state is never a mixture of partially removed old output and partially created new output. Transaction design must protect the ownership transition.
