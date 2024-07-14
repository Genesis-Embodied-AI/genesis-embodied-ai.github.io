# Getting Start:
### Genesis: A generative universe for general-purpose robotics & embodied AI learning.

*Our Top-1 Principle: We must make it SUPER EASY to use.*

## Install
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

4. (Optional) If you need `nvisii` raytracer and denoising:
```
pip install nvisii
pip install oidn
```

5. (Optional) If you need the built-in motion planning capability, download pre-compiled OMPL wheel here: https://github.com/ompl/ompl/releases/tag/prerelease, and then pip install it.

6. (Optional) If you need surface reconstruction with splashsurf (https://github.com/InteractiveComputerGraphics/splashsurf):
```
cargo install splashsurf
```

## Get started
See files under `examples/` to go through the whole forward-backward pipeline.

## genesis.Tensor()
We now have our own tensor data types: `genesis.Tensor()`, for the following reasons:
- to ensure a consistent user experience :)
- it enables end-to-end gradient flow from loss all the way back to action input
- it removes need for specifying datatype (though you still can) when creaing tensors. The datatype specified when calling gs.init() will be used when creating genesis tensors.
- provides additional safety checks, such as contiguous check and check if tensors from different Scene are accidently being merged into the same computation graph.
- supports other potential customizations if we need.

This is essentially a subclass of pytorch tensors, so users can simply treat it as torch tensors and apply different kinds of torch operations.

In pytorch, the recommended way of creating tensors is to call `torch.tensor` and other tensor creation ops like `torch.rand`, `torch.zeros`, `torch.from_numpy`, etc. We aim to reproduce the same experience in genesis, and genesis tensors can be created simply by replacing `torch` with `genesis`, e.g.
```
x = gs.tensor([0.5, 0.73, 0.5])
y = gs.rand(size=(horizon, 3), requires_grad=True)
```
Tensors created this way are leaf tensors, and their gradient can be accessed with `tensor.grad` after backward pass. Pytorch operations mixing torch and genesis tensors will automatically yield genesis tensors.

There exist a few minor differences though:
- Similar to torch, genesis tensor creation automatically infers the data type of the tensor based on the parameter (whether int or float), but then will convert to the float or int type with precision specified in gs.init().
- Users can also can override datatypes, and now we support `dtype=int` or `dtype=float` when calling the tensor creation ops.
- All genesis tensors are on cuda, so device selection is not allowed. This means unlike `torch.from_numpy`, `genesis.from_numpy` directly gives you tensors on cuda.
- `genesis.from_torch(detach=True)`: this creates a genesis tensor given a torch tensor. When calling this, if detach is True, the returned genesis tensor will be a new leaf node, detached from pytorch's computation graph. If detach is False, the returned genesis tensor will be connected to the upstream torch computation graph, and when calling backward pass, the gradient will flow back all the way to connected pytorch tensors. By default, we should use purely genesis tensors, but this allows potential integration with upstream applications built on pytorch, e.g. training neural policies.
- Each genesis tensor object has a `scene` attribute. Any child tensors derived from it would inherit the same scene. This lets us keep track of the source of the gradient flow.
    ```python
    state = scene.get_state()
    # state.pos is a genesis tensor
    print(state.pos.scene) # output: <class 'genesis.engine.scene.Scene'> id: 'e1a95be2-0947-4dcb-ad02-47b8541df0a0'
    random_tensor = gs.rand(size=(), requires_grad=True)
    print(random_tensor.scene) # output: None
    pos_ = state.pos + random_tensor
    print(pos_.scene) # output: <class 'genesis.engine.scene.Scene'> id: 'e1a95be2-0947-4dcb-ad02-47b8541df0a0'
    ```
    If `tensor.scene` is `None`, `tensor.backward()` behaves identically to a torch tensor's `backward()`. Otherwise, it will allow gradient flow back to `tensor.scene` and trigger upstream gradient flow.
- To resemble torch's behavior like `nn.Module.zero_grad()` or `optimizer.zero_grad()`, you can also do `tensor.zero_grad()` with a genesis tensor.

**NB**: This is just a initial implementation and subject to future changes if needed. Suggestions are welcome.


## Guidelines
(I will keep updating this)
- Use genesis.tensor whenever possible. Note that when we pass genesis tensor to taichi kernels, call tensor.assert_contiguous() to check whether it's contiguous, since taichi only support contiguous external tensor.
- Don't expose any taichi-related usage to user.
- When you add new class, also implement `__repr__()` for easier interactive debugging. (See genesis/engine/states.py as an example)
- Contain all simulation-related stuff in genesis.engine.
- Use `genesis.logger.info()`/`debug()`/`warning()` instead of `print()`.
- Users will be alerted that it's not recommended to query for scene states too many times and not use them. All accessed scene states will be stored in a scene-level list, and considered part of the computation graph. This list will be freed when calling `scene.reset_grad()`, which frees up all occupied gpu memory.
- Hierarchy - we abstract each level of entity creation, so they are unified and independent of each other:
    - physics solver: we support various types: MPM, PBD, SPH, FEM, rigid, etc. The idea is to let user flexibly choose among them, without having to change any front-end APIs
    - material -> this determined backend physics solver. we will have MPMLiquid, SPHLiquid, PBDLiquid, MPMElastic, FEMElastic, etc.
    - geom -> this defines the entity's geometry. Can be either one of the shape primitives, or from mesh, or from URDF, etc. These geometries are independent of solver used.
    - all different entities are added via the same `scene.add_entity()`
- default solver order (for code consistency)
    - rigid
    - avatar
    - mpm
    - sph
    - pbd
    - fem
    - sf
- Some order convention
    - quaternion: `[w, x, y, z]`
    - euler
        - user input: we use extrinsic x-y-z, in `degree`, as this is more intuitive
            - interpretation: we use `scipy.Rotation`'s `xyz` ordering.
        - internal xyz:
            - euler is defined differently in various sources
            - in our case, we use xyz to refer to the intrinsic rotation in order x-y-z, same as mujoco. Note that this aligns with others, e.f. dof force and position.
            - for angular velocity, we use rotvec.
- We use `id` for each object's `uuid`, and `idx` for its index
- `uv` order
    - assimp, trimesh: from bottom left corner
    - ours, pygltflib, luisa: from top left corner
- sim options vs solver options
    - for any parameter that exists both in sim and solver options, the one in solver options has a higher priority, and will be initialized using the value in sim options when undefined
    - the recommended way is to define dt via sim options, so that all solvers operate in the same temporal scale. However, users can also set different dt for different solvers
    - RigidSolver operates at step level, and all other solvers operate at substep level. In order to make them compatible, all non-rigid solvers use `substeps` in sim options.
- Some design and conventions for rigid solver
    - for attribute, we use `*_idx`. e.g. `link_info.parent_idx`
    - for id inside loop iteration, we use `i_*`.
    - For all variables inside kernel loop
        - suffix abbreviation:
            - `i_l`: link id
            - `i_p`: parent link id
            - `i_r`: root link id
            - `i_g`: geom id
            - `i_d`: dof
        - for prefix, we use:
            - `l_`: link
            - `l_info`: links_info[i_l]
            - `g_`: geom
            - `p_`: parent
            - ...
    - on index storing
        - Do we store offset-ed index in each class (`link`, `geom`, etc) or only a local one?
            - let's do the former, since when user query e.g., an `entity`, it's better if it shows the global link idx
            - This applies to indexes of `link`, `geom`, `verts`, etc.
        - each object stores
            - its parent class. e.g. `link` stores its `entity`
            - its global `idx` after offset
            - offset value for its own children. e.g. a `geom` only stores `idx_offset_*` for `vert`, `face`, and `edge`.
    - root vs base
        - `root` is a more natural name as we use a tree structure for the links, while `base` is more informative from user's perspective
        - Let's use `root` link inernally, and `base` link for docs etc.
    - root pose vs q (current design subject to possible change)
        - for both arm and single mesh, its pos and euler specified when loaded is its root pose, and q will be w.r.t to this
    - control interface
        - root pos should be merged with joint pos of the first (fixed) joint, which connectes world to the first link
        - if we want to control something, we will command velocity
            - thus, if it’s a free joint, it’s ok. We will override velocity
            - if it’s a fixed joint, there’s no dof for it, so we cannot control it
        - if we need position control, we will write a PD controller and send velocity command under the hood.
        - we can still change root pos (first joint pos), even if its fixed. but this is NOT recommended.
            - this applies to both fixed and free joint. Not recommended in both case, because even it’s free joint, setting position will violate physics.
            - then what’s the difference between free and fixed joint?
                - the free dof will be affected by external effect, while the fixed joint won’t
    - mjcf vs urdf
        - mjcf xml always has a worldbody, so we will skip it. sometimes this worldbody has some geoms associated, let’s not support it for now.
        - urdf only has robot, so we will load everything. Sometimes the robot can have a included world link, then it will be loaded into genesis's world and be the root link of the entity.
    - collision handling: we store base on convexelized geoms
        - for mesh-based assets, we generate a convex hull of all the groups stored in the mesh
            - this groups can either be originally stored submeshes, or if group_by_material=True, we will group by material
            - each group will be one RigidGeom
        - for mjcf, we convexlize based on mujoco's geoms. Each mj geom will be one RigidGeom
        - for urdf
            - each urdf can contain multiple links, each link contains multiple geometries (collisions and visuals), and each geomtry will be one primitive or one external assset. Since `.obj` contains multiple sub-meshes, one urdf geomtry can have multiple meshes
            - we convexlize this lowest-level mesh and store as RigidGeom
    - control interface design
        - we will not explicitly have concept like `base pose`
            - in pybullet, movable mesh is its own baselink, and when pushed its base pose will change
            - in genesis
                - everything will connect to world (link -1)
                - everything will have root pose. This is the initial pose and will not be changed. This is the reference we use when calculating q.
                - free moving objects will connect to world via a free joint with 6 DoFs. When being pushed, this state will be changed, but its root pose will stay the same.
    - prefix `v`
        - this is used for global parameters that are used for visualization (visual geoms, verts, edges, normals etc)

## Compiling LuisaRender
- The submodule LuisaRender is under `ext/LuisaRender`:
    ```
    git submodule update --init --recursive
    ```
### Dependencies
**NB**: It seems compilation only works on Ubuntu 20.04+, As vulkan 1.2+ is needed and 18.04 only supports 1.1, but I haven't fully checked this...

- upgrade `g++` and `gcc` to version 11
```
sudo apt install build-essential manpages-dev software-properties-common
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt update && sudo apt install gcc-11 g++-11
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-11 110
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 110

# verify
g++ --version
gcc --version
```
- cmake
```
# if you system's cmake version is under 3.18, uninstall that and reinstall via snap
sudo snap install cmake --classic
```
- CUDA
You need to install a system-wide cuda. 11.7 worked for me. (Update: 4090 requires 11.8+)
    - download https://developer.nvidia.com/cuda-11-7-0-download-archive
    - Install cuda toolkit.
    - reboot
- Vulkan
```
sudo apt install libvulkan-dev
```
- rust
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# if the above gives downloader error, make sure your curl was installed via apt, not snap
```
- zlib
```
sudo apt-get install zlib1g-dev
```
- X11
```
sudo apt-get install libx11-dev
```
- RandR headers
```
sudo apt-get install xorg-dev libglu1-mesa-dev
```
- pybind
```
pip install "pybind11[global]"
```
### Compile
- Build LuisaRender and its python binding:
    ```
    cd ./ext/LuisaRender
    cmake -S . -B build -D CMAKE_BUILD_TYPE=Release -D PYTHON_VERSIONS=3.9 -D LUISA_COMPUTE_DOWNLOAD_NVCOMP=ON
    cmake --build build -j $(nproc)
    ```
- add to your `.bashrc` (TODO: this seems not really needed?)
    ```
    echo "export LD_LIBRARY_PATH=${PWD}/build/bin:\$LD_LIBRARY_PATH" >> ~/.bashrc
    source ~/.bashrc
    ```
- If you encounterd this error: "`GLIBCXX_3.4.30` not found"
```
cd ~/anaconda3/envs/genesis/lib
mv libstdc++.so.6 libstdc++.so.6.old
ln -s /usr/lib/x86_64-linux-gnu/libstdc++.so.6 libstdc++.so.6
```

## Compiling ParticleMesher
### Dependencies
- OpenVDB
    Follow https://www.openvdb.org/documentation/doxygen/build.html to install OpenVDB (Building Standalone).
    You should first clone OpenVDB repository a place you wish to install:
    ```
    git clone https://github.com/AcademySoftwareFoundation/openvdb.git
    ```
    Then install dependencies that building OpenVDB requires:
    ```
    sudo apt-get install cmake                   # CMake
    sudo apt-get install libtbb-dev              # TBB
    sudo apt-get install zlibc                   # zlib
    sudo apt-get install libboost-iostreams-dev  # Boost::iostream
    sudo apt-get install libblosc-dev            # Blosc
    ```
    Then build and install OpenVDB:
    ```
    cd openvdb
    mkdir build
    cd build
    cmake ../
    cmake --build .
    cmake --build . --target install
    ```
    If there's complaint about boost version: https://stackoverflow.com/a/75893242

    After installation you will find your OpenVDB module path. It is often `/usr/local/lib/cmake/OpenVDB`
### Compile
- Build ParticleMesher and its python binding:
    ```
    cd ./ext/ParticleMesher
    cmake -S . -B build -D CMAKE_BUILD_TYPE=Release -D PYTHON_VERSIONS=3.9 -D OPENVDB_MODULE_PATH=[OpenVDB module path]
    cmake --build build -j $(nproc)
    ```
- add to your `.bashrc`
    ```
    echo "export PMP_PATH=${PWD}/build/bin" >> ~/.bashrc
    echo "export LD_LIBRARY_PATH=${PWD}/build/bin:$LD_LIBRARY_PATH" >> ~/.bashrc
    source ~/.bashrc
    ```