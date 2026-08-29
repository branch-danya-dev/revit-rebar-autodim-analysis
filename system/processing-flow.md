# Rebar AutoDim — End-to-End Processing Flow

This is the system-synthesis view of one command execution.

It connects local responsibility areas without making this file the owner of their detailed rules.

```mermaid
flowchart TD
    U[Structural Engineer runs command] --> C[Execution Context]
    C -->|unsupported view| STOP[Stop before writes]
    C -->|supported view| Z[Discover visible Area Reinforcement]
    Z -->|none| EMPTY[Report valid empty result]
    Z --> LOOP{For each candidate zone}

    LOOP --> E[Evaluate eligibility]
    E -->|unsupported / uncertain| SKIP[Skip zone]
    E -->|eligible| G[Normalize geometry in view frame]
    G --> R[Resolve semantic references]
    R --> L[Build annotation layout intent]
    L --> O[Determine required / conditional annotation targets]
    O --> X[Regeneration ownership check]
    X --> T[Open zone write transaction]
    T --> A[Realize Revit references and annotations]
    A -->|complete valid result| M[Store generation ownership metadata]
    M --> COMMIT[Commit zone transaction]
    A -->|required result cannot be realized| ROLLBACK[Rollback zone transaction]

    SKIP --> NEXT[Next zone]
    COMMIT --> NEXT
    ROLLBACK --> NEXT
    NEXT --> LOOP

    LOOP -->|done| REPORT[Aggregate and report processing result]
```

## Responsibility handoffs

| Step | Canonical owner | Output / decision |
|---|---|---|
| View validation and discovery | [`execution-context/`](../execution-context/README.md) | supported context + candidate zones |
| Zone interpretation | [`geometry/`](../geometry/README.md) | normalized `ZoneGeometry` |
| What should be referenced | [`references/`](../references/README.md) | semantic boundary/grid targets |
| Where annotation should appear | [`layout/`](../layout/README.md) | deterministic layout intent |
| What constitutes the generated result | [`annotations/`](../annotations/README.md) | mandatory and conditional targets |
| Which existing output belongs to the zone | [`regeneration/`](../regeneration/README.md) | previous/current result ownership |
| Native Revit realization | [`revit-boundary/`](../revit-boundary/README.md) | valid native elements or rollback |
| Cross-system outcome | [`system/processing-outcomes.md`](processing-outcomes.md) | zone + command result semantics |

## Important boundary

This flow does not mean each responsibility must map to one implementation class.

It describes semantic ownership. The implementation may combine or split code as long as these system meanings remain explicit and consistent.
