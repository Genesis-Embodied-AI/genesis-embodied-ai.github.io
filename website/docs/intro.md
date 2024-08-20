---
sidebar_position: 0
---

# What is Genesis?

## Overview

Genesis is a physical platform designed for general purpose *Robotics/Embodied AI/Physical AI* applications. It is simultaneously multiple things:
1. A **universal physics engine** re-built from the ground up, capable of simulating a wide range of materials and physical phenomena.
2. A **lightweight**, **ultra-fast**, **pythonic**, **user-friendly**, yet **comprehensive** robotics simulation platform.
3. A powerful and fast **photo-realistic rendering system**.
3. A **generative data engine** that transforms user-prompted natural language description into various modalities of data.


Powered by a universal physics engine re-designed and re-built from the ground up, Genesis integrates various physics solvers and their coupling into a unified framework. This core physics engine is further enhanced by a generative agent framework that operates at an upper level, aiming towards fully **automated data generation** for robotics and beyond.

Genesis is built with the following ***long-term missions***:
1. **Lowering the barrier** to using physics simulations and making robotics research accessible to everyone. (See our promise here)
2. **Unifying a wide spectrum of state-of-the-art physics solvers** into a single framework, allowing re-creating the whole physical world in a virtual realm with the highest possible physical, visual and sensory fidelity, using the most up-to-date simulation techniques.
3. **Minimizing human effort** in collecting and generating data for robotics and other domains, letting the data flywheel spin on its own.

## Why another robotics simulator?
Compared to prior simulation platforms, here we highlight several key features of Genesis:
- 🐍 **100% Python**, both front-end interface and back-end physics engine, all natively developped in python.
- 👶 **Effortless installation** and an **intuitive and friendly API** design.
- 🚀 **Parallelized simulation** with ***unprecedented speed*** and : Genesis is the **world's fastest** physics engine, achieving up to ***20x*** (yes, not a typo) simulation speed compared to existing GPU-accelerated robotic simulators (Isaac Gym/Sim/Lab, Mujoco MJX, etc).
- 💥 A **unified** framework that supports various state-of-the-art physics solvers, modeling **a vast range of materials** and physical phenomena.
- 📸 Fast and photo-realistic rendering.
- ☝🏻 Physically-accurate and differentiable **tactile sensor**.
- 🌌 Native support for ***[Generative Simulation](https://arxiv.org/abs/2305.10455)***, allowing **language-prompted data generation** of various modalities (*interactive scene*, *task*, *reward*, *assets*, *motion*, *policy*, *trajectory*, etc.).

