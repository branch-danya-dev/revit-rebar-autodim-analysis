# Annotation Layout

This area owns **where intended dimensions are placed around a valid zone**.

## Canonical question

> Given valid geometry and semantic targets, where should each generated dimension appear so the result is deterministic and readable?

## Owned knowledge

- preferred side policy;
- base spacing and view-scale conversion;
- independent same-side offset stacks;
- deterministic supported crop fallback;
- same-zone collision scope.

## Default intent

```text
Overall width  → Bottom
Overall height → Right
Left grid      → Left
Right grid     → Right
Above grid     → Top
Below grid     → Bottom
```

Only annotation targets that actually exist reserve an offset level.

## Canonical detail

→ [`placement-policy.md`](placement-policy.md) — side policy, offset levels, scale awareness, collision boundary, verification and legacy anchors.

## Does not own

- zone geometry → [`../geometry/`](../geometry/);
- whether a grid target exists → [`../references/`](../references/);
- native element creation → [`../revit-boundary/`](../revit-boundary/).

The current system solves predictable collisions among dimensions generated for one source zone. It is not a general drawing-wide annotation collision solver.
