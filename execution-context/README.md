# Execution Context

This area owns the canonical meaning of **what one Rebar AutoDim command execution is allowed to process and how the active view frames that work**.

It answers:

> **Which Revit context is active, which source zones belong to this run, and which local coordinate frame should downstream analysis use?**

## Owned knowledge

Execution Context owns:

- active Revit document and active view as the command scope;
- supported vs unsupported view conditions;
- view-scoped discovery of candidate `Area Reinforcement` elements;
- the local view frame used by downstream reasoning:
  - `Origin`;
  - `RightDirection`;
  - `UpDirection`;
  - `ViewDirection`;
  - view scale;
  - relevant crop information;
- the distinction between an empty valid run and an invalid execution context.

It does **not** own:

- whether a candidate zone has valid rectangular geometry — [`geometry/`](../geometry/);
- which structural grid should be selected — [`references/`](../references/);
- where dimensions should be placed — [`layout/`](../layout/);
- how Revit elements are created — [`revit-boundary/`](../revit-boundary/).

## Context model

```text
Revit document
+
active supported view
+
view-local coordinate frame
+
visible candidate zones
        ↓
EXECUTION CONTEXT
```

Downstream stages should not repeatedly rediscover this context independently.

## View-space authority

For this system, the active view is authoritative for **annotation orientation**.

```text
Model XYZ
   ↓ project through active view
(u, v, w)
```

This does not mean the view owns structural geometry. Revit model geometry remains source evidence; the view owns the frame in which annotation width, height, side and placement are interpreted.

## Candidate-zone rule

Only `Area Reinforcement` visible in the active supported view is eligible for this command execution.

```text
all document reinforcement
        ↓ view scope
visible Area Reinforcement
        ↓ candidate set
```

Candidate does not mean valid. Geometry validation happens later.

## Empty vs invalid execution

These outcomes must remain different:

```text
unsupported view
→ command cannot execute safely
→ stop before writes

supported view + zero candidate zones
→ valid empty result
→ complete with no changes
```

That distinction prevents the absence of work from being treated as a system error.

## Revalidation triggers

Execution context must be rebuilt when a new command starts because any of these may have changed:

- active view;
- visible elements;
- view orientation;
- scale;
- crop region;
- source-zone visibility.

The command should reason from the current Revit state rather than reuse stale context from a previous run.
