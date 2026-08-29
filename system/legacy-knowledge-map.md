# Legacy Knowledge Migration Map

This document records how the current artifact/process-oriented repository will be decomposed into SSAD canonical responsibility owners.

It is a migration/audit aid, not a second source of truth.

## Current repository shape

The existing root is organized by analysis/document sequence:

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
```

That shape is useful as a record of how the analysis was presented, but it mixes multiple system owners inside document categories.

## Migration rule

```text
legacy artifact
→ identify individual system claims
→ assign canonical responsibility owner
→ rewrite/link near that owner
→ preserve useful requirement IDs as traceability evidence
→ remove superseded artifact tree only after coverage is verified
```

## Initial knowledge map

| Legacy source | Main knowledge inside it | Target canonical owners |
|---|---|---|
| `01-Scope-and-Problem/` | problem, system boundary, constraints | `system/`, `execution-context/`, `evidence/` |
| `02-AS-IS-and-TO-BE/` | manual workflow, target processing flow | `system/` + local responsibility flows |
| `03-Requirements/` | geometry, reference, placement, safety, rerun rules | split across `execution-context/`, `geometry/`, `references/`, `layout/`, `annotations/`, `regeneration/`, `revit-boundary/` |
| `04-System-Design/` | components, pipeline, system context | `system/` + implementation evidence linked from local owners |
| `05-Geometry-and-Placement/` | normalized geometry, directional grids, layout | `geometry/`, `references/`, `layout/` |
| `06-Revit-API-Interaction/` | host reads/writes, references, transaction sequence | `revit-boundary/`, `annotations/`, `regeneration/` |
| `07-Errors-and-Rerun/` | skip/continue/rollback behavior, idempotency | local failure semantics + `regeneration/` + `revit-boundary/` |
| `08-Traceability/` | requirement-to-behavior mapping | legacy IDs colocated with canonical owners; optional migration audit |
| `09-Result/` | before/after screenshots and outcome | `evidence/` |

## Why requirements cannot remain one canonical owner

The current `business-rules.md` contains rules from several distinct responsibilities.

Examples:

```text
BR-001 / BR-002
→ execution context + geometry eligibility

BR-003
→ geometry/view-space interpretation

BR-005 / BR-006 / BR-007 / BR-008 / BR-009
→ reference resolution

BR-010 / BR-011
→ layout

BR-012 / BR-013
→ annotation + Revit boundary

BR-014
→ regeneration

BR-015 / BR-016
→ local failure policy + system safety
```

The IDs remain useful traceability anchors, but the file category `Business Rules` should not remain the owner of all those meanings.

## Component model interpretation

The existing implementation model is useful evidence:

```text
Command Handler
View Context
Zone Collector
Geometry Processor
Grid Resolver
Placement Engine
Annotation Generator
Generation Tracker
```

However, SSAD does not require the knowledge hierarchy to mirror class/component names.

For example:

```text
View Context + part of Zone Collector
→ execution-context/

Geometry Processor
→ geometry/

Grid Resolver + reference-realization rules
→ references/

Placement Engine
→ layout/

Annotation Generator
→ annotations/ + revit-boundary/

Generation Tracker
→ regeneration/
```

This allows code to be refactored without forcing the system knowledge architecture to change when the underlying responsibility remains stable.

## Completion criterion

The migration is complete when:

1. every significant system rule has one canonical responsibility owner;
2. requirement IDs remain only as traceability evidence rather than repository architecture;
3. PlantUML/process artifacts are either relocated as owner-local visuals, converted to current Mermaid views or retired;
4. before/after evidence is separated from canonical system meaning;
5. the old numbered artifact tree can be removed without losing active knowledge;
6. the root README navigates by system question rather than by document type.
