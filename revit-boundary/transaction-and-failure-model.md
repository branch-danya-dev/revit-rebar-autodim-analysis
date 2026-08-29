# Rebar AutoDim — Revit Transaction and Failure Model

This document owns **how plugin intent crosses the Autodesk Revit host boundary safely**.

It does not own geometry interpretation, semantic reference selection or placement policy.

## Revit authority

Autodesk Revit is authoritative for:

- active document/view state;
- native element existence;
- validity of `Reference` combinations;
- valid dimension geometry;
- whether element creation/deletion succeeds;
- transaction commit/rollback semantics.

Rebar AutoDim must treat API rejection as real evidence rather than assume its prior analysis guarantees native validity.

## Reads

The plugin reads:

- active-view orientation, scale and crop context;
- visible `Area Reinforcement` identity and geometry;
- visible structural grids and native references;
- existing generation metadata;
- existing plugin-owned annotation output when present.

## Writes

The plugin writes only within its annotation layer:

- supporting detail curves when required;
- native dimensions;
- optional grouping/association elements;
- persistent generation metadata;
- deletion of previous plugin-owned generated output.

It does not modify reinforcement or structural-grid source data.

## Semantic target → native reference

The References owner decides what should be dimensioned.

This boundary decides whether Revit can realize that target.

```text
semantic target
→ obtain/reify native Revit References
→ build valid ReferenceArray
→ provide valid dimension line
→ NewDimension / equivalent native creation
```

Supporting detail curves may be created to expose stable references for zone boundaries when source geometry does not provide suitable dimension references.

## Zone-level transaction boundary

The historical interaction model already used one transaction for each supported source zone.

That aligns atomicity with one independently meaningful generated result:

```text
begin zone transaction
→ remove old plugin-owned result if present
→ create supporting references
→ create mandatory + applicable conditional dimensions
→ store ownership metadata
→ commit
```

If the complete required result cannot be committed safely:

```text
failure
→ rollback zone transaction
→ record/report zone failure
→ continue other zones where possible
```

## Why this boundary matters

If one transaction covered the whole active view, a single invalid zone could force rollback of valid independent zones.

Zone-level transaction isolation better matches the system requirement that local failures remain local where technically possible.

This is a semantic preference grounded in the existing interaction design, not a claim that every future implementation must use one specific Revit transaction helper type.

## Failure classes

### Unsupported command context

Detected before writes. No transaction is opened.

### Invalid source geometry

Detected before write realization. Zone is skipped.

### Missing semantic grid target

Not a failure. No grid dimension is expected for that side.

### Native reference/creation rejection

Revit rejects an intended annotation target or dimension geometry.

If the rejected target is mandatory, the zone result cannot be committed as complete.

If the target is conditional but semantically applicable, the failure must remain visible rather than be silently reclassified as `NOT_APPLICABLE`.

### Transaction failure

The zone write is rolled back. The document must not remain partially committed.

### Previous result missing

Not an API failure by itself. Regeneration treats the old result as already absent and proceeds.

## Fallback rule

A fallback is allowed only when it is already part of supported system behavior and preserves the same semantic meaning.

```text
API workaround
!= permission to change intended system meaning
```

For example, a supporting detail curve may provide a technical reference for the same zone boundary. Fabricating an unrelated structural reference would not be an acceptable fallback.

## Verification

The boundary is correct when:

- source structural data is unchanged;
- all write operations occur in valid Revit transactions;
- failed zone transactions roll back;
- one zone failure does not invalidate unrelated successful zones where isolation is supported;
- native annotation elements are created only from valid references/geometry;
- technical fallback preserves the semantic target.

## Legacy anchors

This owner absorbs:

- Revit realization part of `BR-012`, `BR-013`, `BR-016`;
- Revit realization part of `FR-014`, `FR-017`, `FR-018`;
- `NFR-001`, `NFR-002`, `NFR-006`, `NFR-007`, `NFR-008`;
- `AC-013`, `AC-014`, `AC-015`, `AC-021`.
