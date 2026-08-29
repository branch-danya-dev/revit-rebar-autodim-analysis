# Rebar AutoDim — Processing Outcomes

This document defines what it means for the command, a zone and an individual annotation target to succeed or fail.

The old documentation often used a broad `success / skip / failure` vocabulary. SSAD separates several independent questions.

## Three levels of outcome

```text
Execution Context
→ may the command run on this view?

Zone Eligibility
→ can this source zone be interpreted safely?

Annotation Result
→ which required and conditional annotations were actually committed?
```

## Command-level outcomes

### Unsupported context

The command stops before any write operation when the active view is unsupported.

### Valid empty result

A supported view containing no supported zones is a successful no-op:

```text
supported view
+
zero eligible zones
→ no model changes
→ report empty result
```

### Completed processing

The command may complete with a mix of zone outcomes. One invalid zone does not automatically make the entire command unsuccessful.

The final report should therefore summarize at least conceptually:

```text
zones discovered
zones eligible
zones committed
zones skipped
zone failures
```

The exact UI wording is an implementation concern.

## Zone-level outcomes

### `SKIPPED_UNSUPPORTED`

The element is visible but does not satisfy the supported geometry/model rules.

Examples:

- non-rectangular geometry;
- usable boundary cannot be obtained;
- zero or degenerate dimensions;
- geometry invalid for the active view.

No annotation result is created for this evaluation.

### `COMMITTED`

A valid zone has a transactionally committed current annotation result.

For a committed valid zone, both mandatory overall dimensions exist:

```text
overall width
+
overall height
```

Grid-offset dimensions are conditional and may be absent by design.

### `FAILED_WRITE`

The zone was analytically valid, but the intended annotation result could not be committed safely.

The failed zone transaction is rolled back. The result must not be represented as `COMMITTED` merely because some API calls succeeded before rollback.

## Annotation-target outcomes

Not every missing annotation is a failure.

### Mandatory targets

For a valid zone:

- width dimension;
- height dimension.

If a mandatory target cannot be realized with valid Revit references, the zone cannot satisfy the complete annotation contract and should not be treated as a successful committed result.

### Conditional targets

Grid offsets exist only when their semantic preconditions hold.

```text
valid grid on required side?
    no → NOT_APPLICABLE
   yes
    ↓
coincident with zone boundary?
   yes → NOT_REQUIRED
    no
    ↓
create grid offset dimension
```

Therefore:

```text
missing right grid
!= failed right-grid dimension
```

It means the dimension is not applicable.

## Why this distinction matters

Without separate outcome semantics, these cases look identical:

```text
no grid exists on the right

and

a valid right grid exists but Revit rejects the required dimension
```

They are not equivalent.

The first is an acceptable result. The second is a realization failure that must be visible in the zone outcome.

## Zone transaction boundary

The historical interaction sequence already modeled one transaction per supported zone.

That boundary is useful because it aligns technical atomicity with the smallest independently meaningful generated result:

```text
one source zone
→ one intended annotation result
→ one regeneration ownership relation
→ one write transaction
```

This allows another zone to proceed after a local failure where technically possible.

## Re-run outcome

A re-run is successful only when the ownership relation again points to one current committed result derived from current model state.

```text
old generated result
→ replacement transaction
→ new committed result
```

A failed replacement must not leave an intentionally half-new / half-old state.

Detailed lifecycle rules live in [`../regeneration/result-lifecycle.md`](../regeneration/result-lifecycle.md).
