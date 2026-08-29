# Reference Resolution

This area owns the canonical decision about **what geometry should participate in each generated dimension**.

## Canonical distinction

```text
semantic target
!=
technical Revit Reference
```

The system first decides which zone boundary or directional structural grid should be referenced. Only afterward does the host boundary determine how Revit can realize that target.

## Owned knowledge

- overall boundary targets `Left ↔ Right` and `Bottom ↔ Top`;
- view-relative grid orientation;
- independent `left / right / above / below` grid selection;
- nearest-valid-directional-grid rule;
- missing-grid optionality;
- coincident-grid / zero-offset semantics.

## Core rule

```text
nearest grid globally
!=
nearest valid grid for a side
```

A missing side is `NOT_APPLICABLE`; a coincident selected grid is `NOT_REQUIRED`. Neither state is a failed Revit write.

## Canonical detail

→ [`reference-policy.md`](reference-policy.md) — semantic targets, directional search, optionality, coincident references, native realization boundary and legacy anchors.

## Does not own

- normalized zone geometry → [`../geometry/`](../geometry/);
- placement → [`../layout/`](../layout/);
- native reference validity → [`../revit-boundary/`](../revit-boundary/).

A technical workaround such as a supporting detail curve may realize an already-selected target, but it must not change what the dimension is intended to mean.
