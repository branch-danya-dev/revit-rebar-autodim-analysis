# Rebar AutoDim — Legacy Traceability Audit

This document records where the old requirement identifiers moved during the SSAD restructuring.

It is an **audit aid**, not a second source of truth.

## New traceability direction

The legacy model was:

```text
Problem
→ FR
→ BR
→ AC
→ design document
```

The active model is:

```text
observable system claim
→ canonical responsibility owner
→ verification condition
→ historical IDs as evidence anchors
```

The IDs remain useful for proving that no old behavior disappeared, but navigation no longer starts from identifier type.

## Business rules

| Legacy ID | Meaning | Canonical owner |
|---|---|---|
| BR-001 | active-view visible zones only | `execution-context/eligibility.md` |
| BR-002 | rectangular zones only | `execution-context/eligibility.md`, `geometry/zone-model.md` |
| BR-003 | view-relative geometry | `geometry/zone-model.md` |
| BR-004 | mandatory width + height | `annotations/result-model.md` |
| BR-005 | classify grids by orientation | `references/reference-policy.md` |
| BR-006 | resolve four directions independently | `references/reference-policy.md` |
| BR-007 | wrong-side grid is invalid | `references/reference-policy.md` |
| BR-008 | missing grid means no dimension | `references/reference-policy.md`, `annotations/result-model.md` |
| BR-009 | coincident grid needs no zero-length dimension | `references/reference-policy.md` |
| BR-010 | deterministic side policy | `layout/placement-policy.md` |
| BR-011 | same-side dimensions use offset levels | `layout/placement-policy.md` |
| BR-012 | supporting geometry may provide stable references | `references/reference-policy.md`, `revit-boundary/transaction-and-failure-model.md` |
| BR-013 | source structural model is unchanged | `system/invariants.md`, `annotations/result-model.md` |
| BR-014 | re-run replaces old result | `regeneration/result-lifecycle.md` |
| BR-015 | uncertain zones are skipped | `execution-context/eligibility.md`, `geometry/zone-model.md` |
| BR-016 | zone failures remain local where possible | `system/processing-outcomes.md`, `revit-boundary/transaction-and-failure-model.md` |

## Functional requirements

| Legacy ID | Canonical owner |
|---|---|
| FR-001, FR-002, FR-003 | `execution-context/eligibility.md` |
| FR-004, FR-005, FR-006 | `geometry/zone-model.md` |
| FR-007, FR-008 | `references/reference-policy.md` |
| FR-009, FR-010 | `annotations/result-model.md` |
| FR-011 | `references/reference-policy.md` + `annotations/result-model.md` |
| FR-012, FR-013 | `layout/placement-policy.md` |
| FR-014 | `references/reference-policy.md` + `revit-boundary/transaction-and-failure-model.md` |
| FR-015, FR-016 | `execution-context/eligibility.md` |
| FR-017, FR-018 | `annotations/result-model.md` + `revit-boundary/transaction-and-failure-model.md` |
| FR-019, FR-020, FR-021 | `regeneration/result-lifecycle.md` |
| FR-022 | `system/processing-outcomes.md`, `execution-context/eligibility.md` |

## Non-functional requirements

| Legacy ID | Canonical owner |
|---|---|
| NFR-001 | `revit-boundary/transaction-and-failure-model.md` |
| NFR-002 | `system/invariants.md`, `revit-boundary/transaction-and-failure-model.md` |
| NFR-003 | `geometry/zone-model.md` |
| NFR-004 | `regeneration/result-lifecycle.md` |
| NFR-005 | `layout/placement-policy.md` |
| NFR-006 | `system/processing-outcomes.md`, `revit-boundary/transaction-and-failure-model.md` |
| NFR-007, NFR-008 | `revit-boundary/transaction-and-failure-model.md` |
| NFR-009 | `layout/placement-policy.md`, `annotations/result-model.md` |
| NFR-010 | `execution-context/eligibility.md` |
| NFR-011 | `regeneration/result-lifecycle.md`, `annotations/result-model.md` |
| NFR-012 | system-shaped responsibility structure; local owner READMEs |

## Acceptance criteria

| Legacy ID | Canonical verification owner |
|---|---|
| AC-001, AC-002 | `execution-context/eligibility.md` |
| AC-003, AC-004, AC-005 | `geometry/zone-model.md` + `annotations/result-model.md` |
| AC-006, AC-007 | `references/reference-policy.md` |
| AC-008, AC-009, AC-010 | `references/reference-policy.md` + `annotations/result-model.md` |
| AC-011 | `layout/placement-policy.md` |
| AC-012, AC-013 | `execution-context/eligibility.md`, `system/processing-outcomes.md` |
| AC-014, AC-015 | `annotations/result-model.md`, `revit-boundary/transaction-and-failure-model.md` |
| AC-016, AC-017, AC-018 | `regeneration/result-lifecycle.md` |
| AC-019, AC-020 | `execution-context/eligibility.md` |
| AC-021 | `revit-boundary/transaction-and-failure-model.md` |
| AC-022 | `system/processing-outcomes.md` |

## Important correction discovered during migration

The old artifacts could make these two conditions look like the same missing output:

```text
No valid grid exists on one side

vs

A valid grid exists, but Revit cannot create the required dimension
```

SSAD separates them:

```text
NOT_APPLICABLE / NOT_REQUIRED
→ semantic reference policy

FAILED_WRITE
→ native realization / transaction failure
```

This distinction is now canonical in `references/`, `annotations/` and `revit-boundary/`.

## Coverage conclusion

All `BR-001..016`, `FR-001..022`, `NFR-001..012` and `AC-001..022` have an active canonical owner.

The old identifier-based documents can therefore be removed once the surrounding legacy context, diagrams and result evidence are also relocated.
