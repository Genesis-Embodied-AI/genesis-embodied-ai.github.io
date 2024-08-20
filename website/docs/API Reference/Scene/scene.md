---
sidebar_position: 0
description: API reference for _kernel_inverse_kinematics_new.
---

<!-- # `genesis.Scene` -->

export const Highlight = ({children, color}) => (
  <span
    style={{
      backgroundColor: color,
      borderRadius: '7px',
      color: '#fff',
      padding: '7px',
      cursor: 'pointer',
    }}
    onClick={() => {
      alert(`You clicked the color ${color} with label ${children}`);
    }}>
    {children}
  </span>
);

### <Highlight color="#79a2db">class **genesis.Scene**</Highlight>


```python
class genesis.Scene(
  sim_options=SimOptions(), 
  coupler_options=CouplerOptions(), 
  tool_options=ToolOptions(), 
  rigid_options=RigidOptions(), 
  avatar_options=AvatarOptions(), 
  mpm_options=MPMOptions(), 
  sph_options=SPHOptions(), 
  fem_options=FEMOptions(), 
  sf_options=SFOptions(), 
  pbd_options=PBDOptions(), 
  vis_options=VisOptions(), 
  viewer_options=ViewerOptions(), 
  renderer=Rasterizer(), 
  show_FPS=True
)
```

:::tip
- The added light is for visual purpose only and is not an actual `Entity`.
- Light added this way only takes effect for the `RayTracer` renderer, since `Rasterizer` renderer only supports simple lighting effects.
:::

A `genesis.Scene` object wraps all the components of a simulated world.

- **Parameters (option 1)**: 
  - **sim_options** : *gs.options.SimOptions*
    - The options for the whole simulator.
  - **coupler_options** : *gs.options.CouplerOptions*
    - The options for the coupler which handles inter-solver coupling.
  - **tool_options** : *gs.options.ToolOptions*
    - The options for ToolSolver that manages all the Tool entities.
  - **rigid_options** : *gs.options.ToolOptions*
    - The options for RigidSolver which manages all the rigid body entities.

- **Parameters (option 2)**: 
  - `avatar_options` (gs.options.AvatarOptions)
    - The options for AvatarSolver, which manages the avatar entities in the simulation.
  - `mpm_options` (gs.options.MPMOptions)
    - The options for MPM (Material Point Method) solver.
  - `sph_options` (gs.options.SPHOptions)
    - The options for SPH (Smoothed Particle Hydrodynamics) solver.
  - `fem_options` (gs.options.FEMOptions)
    - The options for FEM (Finite Element Method) solver.
  - `sf_options` (gs.options.SFOptions)
    - The options for SF (Stable Fluids) Solver.
  - `pbd_options` (gs.options.PBDOptions)
    - The options for PBD (Position-Based Dynamics) solver.
  - `vis_options` (gs.options.VisOptions)
    - The options for the the visualizer, configuring how the scene should be visualized.
  - `viewer_options` (gs.options.ViewerOptions)
    - The options for the viewer GUI. If you need headless rendering, simply set this to `None`.
  - `renderer` (gs.renderers.Renderer)
    - The renderer backend used by `gs.Camera`. You can choose either `gs.renderers.Rasterizer()` or `gs.renderers.RayTracer()`.
  - `show_FPS` (bool)
    - If True, simulation FPS will be displayed in terminal. Default is `True`.
