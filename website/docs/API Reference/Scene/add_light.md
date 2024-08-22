---
sidebar_position: 1
slug: /api/you_can_change_file_name
---

<!-- # `Scene.add_light` -->
export const Highlight = ({children, color}) => (
  <span
    style={{
      backgroundColor: color,
      borderRadius: '10px',
      color: '#fff',
      padding: '10px',
      cursor: 'pointer',
    }}
    onClick={() => {
      alert(`You clicked the color ${color} with label ${children}`);
    }}>
    {children}
  </span>
);

### <Highlight color="#79a2db">func **Scene.add_light**</Highlight>



```python
Scene.add_entity(
    morph,
    color = (1.0, 1.0, 1.0),
    intensity = 20.0,
):
```

Add a light into the current Scene.

:::tip
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