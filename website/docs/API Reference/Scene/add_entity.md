---
sidebar_position: 1
---

<!-- # `Scene.add_entity` -->

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

<Highlight color="#79a2db">func **Scene.add_entity**</Highlight>
<br />
<br />

```python
Scene.add_entity(
    morph,
    material=gs.materials.Rigid(),
    surface=None,
    visualize_contact=False,
)
```

Add a `genesis.Entity` object into the current Scene.

- **Parameters**:
  - `morph` (gs.morphs.Morph)
    - A `gs.Morph` object specifying morpology of the entity.
  - `material` (gs.materials.*)
    - A `gs.materials.*` object specifying the physical material of the entity. Defaults to `gs.materials.Rigid()`.
  - `surface` (gs.surfaces.*) 
    - A `gs.surfaces.*` object specifying the visual information used for rendering the entity. Defaults to `gs.surfaces.Default()`.
  - `visualize_contact`
    - Whether to visualize the contact forces for the entity. Defaults to `False`.