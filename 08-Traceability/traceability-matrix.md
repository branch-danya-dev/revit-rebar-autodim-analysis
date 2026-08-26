# Rebar AutoDim — Traceability Matrix

## Purpose

This document links requirements, business rules and acceptance criteria.

The matrix helps verify that each important system behavior is both defined and testable.

---

## Traceability Matrix

| Requirement | Business Rule | Acceptance Criteria |
|---|---|---|
| FR-001 Operate on active view | BR-001 | AC-001, AC-019 |
| FR-002 Detect visible Area Reinforcement | BR-001 | AC-001 |
| FR-003 Process rectangular zones | BR-002 | AC-002 |
| FR-004 Determine zone boundaries | BR-003 | AC-005 |
| FR-005 Determine overall width | BR-003, BR-004 | AC-003, AC-005 |
| FR-006 Determine overall height | BR-003, BR-004 | AC-004, AC-005 |
| FR-007 Detect structural grids | BR-005 | AC-006 |
| FR-008 Determine valid grid references | BR-005, BR-006, BR-007 | AC-006, AC-007 |
| FR-009 Create overall width dimension | BR-004 | AC-003 |
| FR-010 Create overall height dimension | BR-004 | AC-004 |
| FR-011 Create grid offset dimensions | BR-006, BR-008, BR-009 | AC-008, AC-009, AC-010 |
| FR-012 Calculate dimension placement | BR-010 | AC-011, AC-018 |
| FR-013 Separate parallel dimensions | BR-011 | AC-011 |
| FR-014 Create supporting detail geometry | BR-012 | AC-015 |
| FR-015 Process all supported visible zones | BR-001, BR-016 | AC-012, AC-013 |
| FR-016 Skip unsupported or invalid zones | BR-002, BR-015 | AC-002, AC-013 |
| FR-017 Preserve source model | BR-013 | AC-014 |
| FR-018 Create native Revit annotations | BR-012 | AC-015 |
| FR-019 Support repeated execution | BR-014 | AC-016, AC-017, AC-018 |
| FR-020 Prevent duplicate annotation sets | BR-014 | AC-016 |
| FR-021 Regenerate from current model state | BR-014 | AC-017 |
| FR-022 Report processing result | — | AC-020, AC-022 |

---

## Non-Functional Coverage

| Requirement | Covered By |
|---|---|
| NFR-001 Revit 2025 compatibility | Solution scope and execution environment |
| NFR-002 Model integrity | AC-014 |
| NFR-003 View-based geometry | AC-005 |
| NFR-004 Deterministic output | AC-018 |
| NFR-005 Consistent placement | AC-011, AC-018 |
| NFR-006 Isolated zone failures | AC-013 |
| NFR-007 Valid Revit transactions | AC-021 |
| NFR-008 Transaction rollback safety | AC-021 |
| NFR-009 Annotation readability | AC-011 |
| NFR-010 Interactive processing | AC-012 |
| NFR-011 Generated result identification | AC-016, AC-017 |
| NFR-012 Logical separation of concerns | Architecture review |

---

## Example Trace

```text
Problem
  ↓
Manual grid selection is repetitive and error-prone
  ↓
FR-008
Determine valid grid references
  ↓
BR-006 / BR-007
Select grids independently by direction
  ↓
AC-007
Only grids on the required side are considered
  ↓
Grid Selection Design

Coverage Gaps

The following areas are intentionally outside the current traceability scope:

wall-based references;
non-rectangular reinforcement zones;
multi-view batch processing;
full annotation collision solving.

They are treated as future extensions rather than missing requirements.

Summary

The traceability chain is:

Problem
   ↓
Requirement
   ↓
Business Rule
   ↓
Acceptance Criterion
   ↓
System Design

This keeps the analytical documentation connected to observable plugin behavior.