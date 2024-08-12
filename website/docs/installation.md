---
sidebar_position: 1
title: "Installation"
---

Genesis is designed to be *cross-platform*, supporting both *CPU* and *GPU* (both CUDA and non-CUDA) backends. That said, it is recommended to use Linux platform with CUDA-compatible GPU to achieve the best performance.

## Prerequisites
* Python: 3.7+
* OS
  * Linux (recommended)
  * MacOS / Windows (can run but not fully supported)

## Installation
### Genesis

(Later)
1. The main Genesis package is available via PyPI:
```
pip install genesis
```
2. Install [PyTorch](https://pytorch.org) (>=2.0.0) following the official instructions: https://pytorch.org/get-started/locally/.


(Now)
1. create conda env
```
conda create -n genesis python=3.9
conda activate genesis
```

2. Install taichi and genesis.
Note that at release time, taichi will be a dependency automatically handled. For now, since we rely on some features in the nightly version, let's install nightly manually.
```
pip install -i https://pypi.taichi.graphics/simple/ taichi-nightly
pip install -e .
```

3. Install torch (>=2.0.0) following the official instructions: https://pytorch.org/get-started/locally/


### (Optional) Motion planning
Genesis integrated OMPL's motion planning functionalities and wraps it using a intuitive API for effortless motion planning. If you need the built-in motion planning capability, download pre-compiled OMPL wheel [here](https://github.com/ompl/ompl/releases/tag/prerelease), and then `pip install` it.

### (Optional) Surface reconstruction
If you need fancy visuals for visualizing particle-based entities (fluids, deformables, etc.), you typically need to reconstruct the mesh surface using the internal particle-based representation. We use [splashsurf](https://github.com/InteractiveComputerGraphics/splashsurf), a state-of-the-art surface reconstruction method for achieving this:
```
cargo install splashsurf
```

### (Optional) Ray Tracing Renderer

If you need photo-realistic visuals, Genesis includes a ray-tracing (path-tracing) based renderer, developped using [LuisaCompute](https://github.com/LuisaGroup/LuisaCompute), a high-performance domain specific language designed for rendering.

blahblahs


