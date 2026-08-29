# Rebar AutoDim — Execution Context and Eligibility

This document owns the rules that determine **where the command may run and which source elements enter deeper analysis**.

## Command context

The command is scoped to one active Revit view.

```text
Active document
+
Active view
→ execution context
```

The active view provides both processing scope and the local annotation frame used later by geometry and layout logic.

## Supported view

An unsupported view stops the command before any model write.

Known unsupported examples include:

- 3D views;
- drafting views;
- other view types that cannot provide the required model/annotation context.

A supported view must allow the plugin to obtain the geometry and annotation-plane information required by the downstream model.

## Candidate discovery

On a supported view, the plugin discovers visible `Area Reinforcement` elements without requiring manual zone selection.

```text
supported active view
→ visible Area Reinforcement
→ candidate zones
```

Only view-visible elements are part of this command execution.

## Empty result

No candidates is not an error:

```text
supported view
+
no visible supported zones
→ no changes
→ report valid empty result
```

## Zone eligibility

A candidate is eligible for the current system behavior only when it can be represented as a supported rectangular zone.

The deeper geometry owner performs the actual geometric proof. Execution Context only owns the eligibility gate and the decision to continue or skip.

### Eligible

```text
visible Area Reinforcement
+
supported context
+
valid rectangular interpretation
→ continue
```

### Unsupported / invalid

```text
candidate exists
+
geometry cannot be proven safely
→ skip zone
→ continue with other candidates
```

The plugin does not approximate unsupported geometry.

## Batch semantics

One command applies the same deterministic rules to all eligible zones visible on the active view.

```text
1 active view
→ N candidate zones
→ independent zone evaluations
→ aggregate command report
```

Failure or invalidity of one zone should remain local where transaction/API constraints permit.

## Out of scope

The current command does not own:

- reinforcement calculation or creation;
- non-rectangular zone analysis;
- multi-view/sheet batch execution;
- user-selected subset processing as the primary workflow;
- full drawing-wide annotation collision solving.

## Legacy anchors

This owner absorbs the system meaning of:

- `BR-001`, `BR-002`, `BR-015`, `BR-016`;
- `FR-001`, `FR-002`, `FR-003`, `FR-015`, `FR-016`, `FR-022`;
- `NFR-006`, `NFR-010`;
- `AC-001`, `AC-002`, `AC-012`, `AC-013`, `AC-019`, `AC-020`, `AC-022`.

Historical mapping: [`../evidence/legacy-traceability.md`](../evidence/legacy-traceability.md).
