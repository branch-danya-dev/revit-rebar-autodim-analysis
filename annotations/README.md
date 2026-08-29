# Generated Annotations

This area owns **what one current plugin-generated annotation result means for one eligible source zone**.

## Canonical result

```text
Generated Annotation Result
├── mandatory Overall Width
├── mandatory Overall Height
├── optional Left Grid Offset
├── optional Right Grid Offset
├── optional Above Grid Offset
├── optional Below Grid Offset
├── supporting detail geometry when required
└── ownership metadata / association
```

The result lives in the native Revit annotation layer. Source reinforcement and structural grid geometry remain read-only.

## Important distinction

```text
conditional target absent
!=
required target failed to realize
```

A zone can be complete without a right-grid dimension when no valid right-side grid exists. It cannot be called complete if its mandatory width or height dimension could not be created safely.

## Canonical detail

→ [`result-model.md`](result-model.md) — mandatory vs conditional content, native-result semantics, completeness verification and legacy anchors.

## Does not own

- geometry meaning → [`../geometry/`](../geometry/);
- reference selection → [`../references/`](../references/);
- placement policy → [`../layout/`](../layout/);
- which result is current across reruns → [`../regeneration/`](../regeneration/);
- transaction/native validity → [`../revit-boundary/`](../revit-boundary/).

Supporting detail curves are plugin-owned technical support for annotation references; they do not become a second structural model.
