# Annotation Layout

This area owns **which dimension intents occupy which sides and offset levels around a valid zone**.

It answers:

> **Given valid geometry and available references, where should each generated dimension be placed so the result remains deterministic and readable?**

## Owned knowledge

Layout owns:

- required overall width/height dimension intent;
- optional directional grid-offset dimension intent after reference resolution;
- preferred side policy;
- base offset policy;
- per-side offset levels for parallel generated dimensions;
- scale-aware spacing;
- supported deterministic fallback when crop constraints make a preferred side unavailable;
- the scope of collision handling.

It does **not** own:

- zone geometry — [`geometry/`](../geometry/);
- which grid/reference is selected — [`references/`](../references/);
- creation of native Revit dimension elements — [`annotations/`](../annotations/);
- arbitrary collision solving with unrelated manual annotations.

## Dimension intents

For every valid supported zone:

```text
required
├── Overall Width
└── Overall Height

optional when reference exists and offset is meaningful
├── Left Grid Offset
├── Right Grid Offset
├── Above Grid Offset
└── Below Grid Offset
```

A missing optional grid reference removes only that dimension intent.

## Preferred side policy

Default sides:

| Dimension intent | Preferred side |
|---|---|
| Overall width | Bottom |
| Overall height | Right |
| Left grid offset | Left |
| Right grid offset | Right |
| Above grid offset | Top |
| Below grid offset | Bottom |

This policy is deterministic system knowledge rather than an incidental order of API calls.

## Per-side stacking

Multiple dimension intents may share one side.

```text
zone boundary
    │
    ├── level 1
    ├── level 2
    └── level 3
```

Only dimensions that actually exist reserve a level.

For example, if no below-grid reference exists, no below-grid intent exists and no extra bottom level should be consumed.

## Scale-aware spacing

The user-visible gap is an annotation-space concern while Revit placement uses model units.

```text
desired visual/paper gap
        ↓
active view scale
        ↓
model-space offset
```

Execution Context provides the scale; Layout owns how that scale affects placement intent.

## Crop constraints

A preferred side may conflict with the usable annotation area of the active view.

Where an alternative is explicitly supported, fallback must remain deterministic.

```text
preferred side unavailable
        ↓
allowed fallback policy
        ↓
alternative side
```

The system must not arbitrarily choose a side based on incidental element ordering.

## Collision scope

Layout owns collisions between dimensions generated for the **same source zone**.

It does not claim to solve all possible collisions with:

- dimensions generated for neighboring zones;
- manually authored dimensions;
- text notes;
- tags;
- unrelated annotation elements.

That boundary should remain explicit instead of implying a general-purpose annotation optimizer.

## Output model

Conceptually, layout produces:

```text
DimensionPlacement
├── Intent
├── Side
├── OffsetLevel
├── StartPoint
├── EndPoint
└── DimensionLine
```

This is placement intent. It is not yet the native Revit element.
