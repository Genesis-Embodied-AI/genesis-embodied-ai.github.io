# 👋🏻 Hello, Genesis

## Prerequisites
* **Python**: 3.7+
* **OS**: Linux (*recommended*) / MacOS / Windows

## System Support
Genesis is designed to be *cross-platform*, supporting both *CPU* and *GPU* (both CUDA and non-CUDA) backends. That said, it is recommended to use Linux platform with CUDA-compatible GPU to achieve the best performance.

<div style="text-align: center;">

| System  | GPU Device        | GPU Simulation | CPU Simulation | On-screen Viewer | Headless Rendering |
| ------- | ----------------- | -------------- | -------------- | ---------------- | ------------------ |
| Linux   | Nvidia            | ✅             | ✅             | ✅               | ✅                 |
|         | AMD               | ✅             | ✅             | ✅               | ✅                 |
| MacOS   | Apple Silicon     | ✅             | ✅             | ✅               | ✅                 |
| Windows | Nvidia            | ✅             | ✅             | ❌               | ❌                 |
|         | AMD               | ✅             | ✅             | ❌               | ❌                 |

</div>

## Installation
1. create conda env
```
conda create -n genesis python=3.9
conda activate genesis
```

2. Install genesis. (will be distributed via pip in public release)
```
pip install -e .
```

3. Install **torch (>=2.0.0)** following the [official instructions](https://pytorch.org/get-started/locally/).


### (Optional) Motion planning
Genesis integrated OMPL's motion planning functionalities and wraps it using a intuitive API for effortless motion planning. If you need the built-in motion planning capability, download pre-compiled OMPL wheel [here](https://github.com/ompl/ompl/releases/tag/prerelease), and then `pip install` it.

### (Optional) Surface reconstruction
If you need fancy visuals for visualizing particle-based entities (fluids, deformables, etc.), you typically need to reconstruct the mesh surface using the internal particle-based representation. We provide two options for this purpose:

1. [splashsurf](https://github.com/InteractiveComputerGraphics/splashsurf), a state-of-the-art surface reconstruction method for achieving this:
```
cargo install splashsurf
```
2. ParticleMesher, our own openVDB-based surface reconstruction tool (faster but with not as smooth):
```
echo "export LD_LIBRARY_PATH=${PWD}/ext/ParticleMesher/ParticleMesherPy:$LD_LIBRARY_PATH" >> ~/.bashrc
source ~/.bashrc
```


### (Optional) Ray Tracing Renderer

If you need photo-realistic visuals, Genesis has a built-in a ray-tracing (path-tracing) based renderer developped using [LuisaCompute](https://github.com/LuisaGroup/LuisaCompute), a high-performance domain specific language designed for rendering.

#### 1. Get LuisaRender
The submodule LuisaRender is under `ext/LuisaRender`:
```
git submodule update --init --recursive
```
#### 2. Dependencies
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
    - You need to install a system-wide cuda. 11.7 worked for me. (Update: 4090 requires 11.8+)
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
    sudo apt-get install patchelf
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
- libsnappy
    ```
    sudo apt-get install libsnappy-dev
    ```
#### 3. Compile
- Build LuisaRender and its python binding:
    ```
    cd ./ext/LuisaRender
    cmake -S . -B build -D CMAKE_BUILD_TYPE=Release -D PYTHON_VERSIONS=3.9 -D LUISA_COMPUTE_DOWNLOAD_NVCOMP=ON -D LUISA_COMPUTE_DOWNLOAD_OIDN=ON
    cmake --build build -j $(nproc)
    ```
- If you encounterd this error: "`GLIBCXX_3.4.30` not found"
    ```
    cd ~/anaconda3/envs/genesis/lib
    mv libstdc++.so.6 libstdc++.so.6.old
    ln -s /usr/lib/x86_64-linux-gnu/libstdc++.so.6 libstdc++.so.6
    ```


