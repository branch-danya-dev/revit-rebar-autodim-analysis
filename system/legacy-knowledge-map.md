# Legacy Knowledge Migration Audit

This document records how the original numbered, artifact-oriented repository was decomposed into SSAD canonical responsibility owners.

It is a historical audit aid, not a second source of truth.

## Status

```text
01-Scope-and-Problem/
02-AS-IS-and-TO-BE/
03-Requirements/
04-System-Design/
05-Geometry-and-Placement/
06-Revit-API-Interaction/
07-Errors-and-Rerun/
08-Traceability/
09-Result/
        ↓
claims decomposed by meaning
        ↓
canonical owner assigned
        ↓
traceability retained as evidence
        ↓
legacy tree retired
```

Historical versions remain available in Git history.

## Migration rule used

```text
legacy artifact
→ identify individual system claims
→ assign canonical responsibility owner
→ rewrite near that owner
→ preserve only useful traceability/evidence
→ remove superseded artifact tree
```

## Final ownership map

| Removed legacy area | Canonical destination |
|---|---|
| Scope / problem / system context | `system/`, `execution-context/`, `evidence/` |
| AS-IS / TO-BE workflows | `system/processing-flow.md`, `evidence/outcome.md` |
| Business rules / FR / NFR / AC | split across local responsibility owners; audit in `evidence/legacy-traceability.md` |
| Component/system design | system synthesis + local owner boundaries; implementation class names are no longer knowledge hierarchy |
| Geometry processing | `geometry/zone-model.md` |
| Grid selection / semantic references | `references/reference-policy.md` |
| Dimension placement | `layout/placement-policy.md` |
| Generated output semantics | `annotations/result-model.md` |
| Re-run / idempotency | `regeneration/result-lifecycle.md` |
| Revit API interaction / transaction sequence | `revit-boundary/transaction-and-failure-model.md` |
| Edge cases | local owners + `system/processing-outcomes.md` |
| Requirement traceability matrix | `evidence/legacy-traceability.md` |
| Before/after / project outcome | `evidence/` |
| PlantUML process/context diagrams | current owner-local Markdown/Mermaid system views |

## What changed analytically

The migration did more than relocate files.

### Document type stopped being ownership

A single old `business-rules.md` contained claims belonging to execution context, geometry, references, layout, annotations, regeneration and host safety. Those claims now live with the responsibility that can explain and verify them.

### Semantic references were separated from Revit references

```text
what should be dimensioned
!=
how Revit can technically reference it
```

This keeps API workarounds from becoming owners of structural meaning.

### Missing optional output was separated from failed output

```text
no valid grid exists
→ NOT_APPLICABLE

valid target exists but native realization fails
→ FAILED_WRITE
```

This distinction is now explicit across `references/`, `annotations/` and `revit-boundary/`.

### Zone outcome was separated from command outcome

A command can complete with valid committed zones, skipped unsupported zones and isolated failures. One broad `success/failure` label is insufficient for the system.

### Transaction scope was aligned with result ownership

The historical Revit interaction sequence already used one write transaction per supported zone. The canonical model now explains why that boundary matters: one source zone maps to one independently meaningful generated result.

## Legacy ID coverage

All historical identifiers are accounted for:

```text
BR-001..016
FR-001..022
NFR-001..012
AC-001..022
```

See [`../evidence/legacy-traceability.md`](../evidence/legacy-traceability.md).

These IDs are traceability anchors only. They no longer define repository navigation or canonical ownership.

## Completion result

The structural migration is complete when the active branch contains only system-shaped knowledge areas and supporting evidence.

After the legacy deletion commit, future changes should be evidence-driven changes to the system model rather than further taxonomy migration.
