# Zone Geometry

This area owns the canonical interpretation of a supported `Area Reinforcement` zone as geometry the annotation system can reason about.

## Canonical question

> What geometric zone is being dimensioned in the active view, and is that interpretation trustworthy enough to annotate?

## Owned knowledge

- projection of source evidence into the active-view frame;
- supported rectangular-zone semantics;
- boundary evidence and supported fallback;
- normalized `Left / Right / Bottom / Top`;
- derived `Width / Height / Center`;
- geometric validity and rejection conditions.

## Canonical model

```text
source Revit geometry
→ active-view projection
→ supported evidence check
→ ZoneGeometry
```

```text
ZoneGeometry
├── Left
├── Right
├── Bottom
├── Top
├── Width
├── Height
└── Center
```

> **Uncertain geometry must not be converted into confident annotation.**

## Canonical detail

→ [`zone-model.md`](zone-model.md) — evidence hierarchy, normalization rules, validity, verification and legacy anchors.

## Does not own

- command candidate discovery → [`../execution-context/`](../execution-context/);
- structural reference selection → [`../references/`](../references/);
- placement → [`../layout/`](../layout/);
- technical dimension references → [`../revit-boundary/`](../revit-boundary/).

`ZoneGeometry` describes what the source zone means. It is not itself a promise that raw source geometry exposes a Revit `Reference` accepted by the dimension API.
