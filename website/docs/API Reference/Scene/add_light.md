---
sidebar_position: 2
---

# `Scene.add_light`

```python
Scene.add_entity(
    morph,
    color = (1.0, 1.0, 1.0),
    intensity = 20.0,
):
```

Add a light into the current Scene.

:::note
- The added light is for visual purpose only and is not an actual `Entity`.
- Light added this way only takes effect for the `RayTracer` renderer, since `Rasterizer` renderer only supports simple lighting effects.
:::

- **Parameters**:
  - `morph` (gs.morphs.Morph)
    - A `gs.Morph` object specifying morpology of the light. This can be either `gs.Morph.Primitive` or `gs.Morph.Mesh`. 
  - `color` (tuple) 
    - a 3-tuple (RGB) representing the light color. Note that this tuple will be normalized.
  - `intensity`
    - the light intensity.