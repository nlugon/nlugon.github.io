---
layout: post
title: The 2025 Landscape of Robotics & Drone Simulation
date: 2025-08-18 10:00:00
description: A critical, practical guide to today’s robotics & UAV simulators
tags: robotics drones simulation autonomy ros usd omniverse unreal unity cesium mujoco gazebo webots isaac-sim airsim pybullet habitat carla
categories: robotics
thumbnail: assets/img/flightgoggles.png
published: true
---


{% include figure.liquid path="assets/img/flightgoggles.png" title="FlightGoggles" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">FlightGoggles Simulator</p>

### 1) Why simulate?

Simulation is no longer just a “nice to have.” It’s the only way to (a) iterate at software speed on algorithms that live in the physical world, (b) generate and replay exact conditions (including “impossible” edge cases), and (c) scale training & validation without burning flight hours or risking hardware. The catch: not all simulators solve the same problems. Below, I map the space, call out strengths/limits, and propose a sane stack for drones—spanning low-level flight controllers to photorealistic, geo-accurate digital twins.

---

### 2) A quick taxonomy (what problem are you solving?)

- **Low-level dynamics**: fast, deterministic physics loops for controls, MPC, estimation.  
- **Manipulation / contact-rich RL**: accurate contact/friction and parallel training throughput.  
- **Perception & sensors**: photoreal rendering, ray-traced LiDAR, RGB-D, distortion models.  
- **Geospatial at Earth scale**: stream the real world into the sim (3D Tiles, heightmaps).  
- **System-of-systems**: replay logs, synchronize many data streams, orchestrate test scenarios.

Keep this taxonomy in mind when choosing tools—your “best” simulator depends on which of these dominates your workload.

---

### 3) Chronology (selected highlights)

- **1998 →** Webots matures into a general, open robot sim (now with ROS 2, OSM import).  
- **2000s →** ODE/Bullet underpin many engines; Gazebo becomes the ROS default (Gazebo Classic EOL’d Jan 2025; migrate to modern Gazebo).  
- **2012 →** MuJoCo debuts as a high-accuracy contact engine (now open-source; rapid releases in 2025).  
- **2017 →** CARLA (driving) and AirSim (UAV/AD) popularize game-engine photoreal sim for autonomy.  
- **2019–2020 →** FlightGoggles (Unreal) and Flightmare (Unity) push photoreal UAV perception & fast dynamics.  
- **2019 →** Habitat accelerates embodied-AI research; Habitat 3.0 targets human-robot interaction.  
- **2020s →** NVIDIA Isaac Sim (Omniverse/OpenUSD) focuses on ROS 2, RTX sensor physics, and robot learning via Isaac Lab.  
- **2023 →** Google’s Photorealistic 3D Tiles become streamable in Cesium for Unreal; not “survey-grade.”  
- **2024–2025 →** “Genesis” (often misheard as “Gemini”) appears as a research-focused, ultra-fast physics stack for robot learning.


---

### 4) Deep(ish) dives — what each does well (and where it bites)

**MuJoCo** — fast, accurate contact dynamics; beloved in RL/manipulation. Rich Python bindings, frequent updates. Weak on out-of-the-box photoreal sensors.

**Gazebo (modern)** — successor to Gazebo Classic (EOL Jan 2025). Better modularity, ROS 2 integration; a great “middle ground” for mobile robots with realistic but not photoreal sensors.

**Webots** — polished, batteries-included sim with ROS 2, docs, and easy OSM import for quick outdoor scenes. Great teaching/rapid prototyping; physics fidelity is fine for many mobile tasks.

**PyBullet/Bullet** — simple Python API, fast; great for RL baselines, legged locomotion research; limited built-in photoreal sensors.

**CoppeliaSim** (V-REP) — broad feature set, integrated path planning and vision, commercial support; strong for lab robots and education.

**Drake** — not a “game engine”; a rigorous controls & dynamics toolbox with MultibodyPlant and excellent optimization tools; complements sims for model-based design and verification.

**Project Chrono** — multiphysics (deformable terrain, granular flows), vehicle dynamics; useful when interaction with soils/suspensions matters (UGVs, off-road).

**RaiSim** — fast, deterministic multi-body engine with bindings and Unity/Unreal visualizers; licensing may matter.

**NVIDIA Isaac Sim (Omniverse/OpenUSD)** — ROS 2 workflow + RTX ray-traced LiDAR/cameras; strong for perception R&D, digital twins, and Isaac Lab robot learning. Requires serious GPUs; production workflows benefit from USD pipelines.

**Unity (Robotics Hub)** — strong editor & tooling; ROS-TCP Connector is straightforward for ROS/ROS 2 bridging. Great for building custom UX and HIL dashboards.

**Unreal Engine** — highest-end visuals; pair with **Cesium for Unreal** to stream Photorealistic 3D Tiles worldwide. Caveats: tiles are *not survey-grade*; licensing and datum alignment matter for validation.

**AirSim** — flexible UAV/UGV sim on Unreal (and historically Unity). Useful APIs for sensors and controls; active GitHub releases.

**CARLA** — the de facto open simulator for autonomous driving research; Unreal-based, good traffic/scenario tooling; ROS bridge built-in.

**Habitat / Habitat-Sim** — embodied-AI at scale with photoreal scans, datasets (HM3D, Replica, etc.); great for navigation/perception policy learning.

**OmniGibson / iGibson** — household manipulation/navigation; OmniGibson builds on Isaac Sim (successor to iGibson).

**ManiSkill (SAPIEN)** — manipulation-first, GPU-parallel data collection; solid for contact-rich tasks and large-scale RL.

**PX4 SITL (jMAVSim / Gazebo)** — essential for low-level flight stack bring-up & CI; jMAVSim is lightweight but community-maintained and Java-dependent.

**FlightGoggles (Unreal)** — photoreal camera sensor streams for UAVs; great for perception benchmarking.

**Flightmare (Unity)** — decoupled rendering/physics for fast quadrotor sims; papers & docs available.

**Genesis (research)** — ambitious, ultra-fast physics for robot learning; promising but still early for enterprise validation workflows—evaluate claims on your workloads.

---

### 5) Unreal or Blender for “digital twins”?

- **Unreal/Unity** are *real-time engines* with asset streaming, LOD, level scripting, and rich runtime APIs; they integrate with ROS and external sims and can run headless or at scale.  
- **Blender** is an *authoring tool* (phenomenal for modeling, baking, rendering) but not designed as your runtime simulator. Use Blender to build assets; run them in Unreal/Unity/Omniverse for interactive testing, sensor simulation, and automation.

For Earth-scale twins, **Cesium for Unreal** can stream Google’s Photorealistic 3D Tiles via Cesium ion, but note Google’s own statement: the tiles are **not “survey-grade.”** For high-precision validation, combine tiles with your own photogrammetry/LiDAR or authoritative GIS.

---

### 6) Version quirks & gotchas you’ll thank yourself for noting

- **Gazebo Classic is EOL (Jan 2025)** → plan migration to modern Gazebo; ROS 2 Jazzy requires modern Gazebo.  
- **PX4 jMAVSim** needs recent Java and is *community-maintained*; treat it as a smoke-test target, not your only sim.  
- **Isaac Sim** ROS 2 workflows and RTX sensors are GPU-heavy; plan on NVIDIA hardware and driver/toolchain parity across CI.  
- **Cesium/Google Tiles**: not survey-grade; do not use as the sole truth source for metric validation.

---

### 7) Big comparison table (2025 snapshot)

> Legend: **Focus** = main sweet spot; **Fidelity** = sensors/graphics; **Scale/Speed** = throughput vs. real-time; **ROS** = ROS/ROS 2 support; **License** = OSS vs. commercial; **Notes** = notable strengths/limits.

| Tool | Focus | Fidelity (Perception) | Scale/Speed | ROS(2) | License | Notes |
|---|---|---|---|---|---|---|
| **MuJoCo** | Dynamics, contact, RL | Low (no native photoreal) | High | Via wrappers | Apache-2.0 | Gold standard for accurate contact |
| **Gazebo (modern)** | Mobile robots | Medium | Medium | **Yes** | Apache-2.0 | Successor to Classic (EOL 2025) |
| **Webots** | General robotics | Medium | Medium | **Yes** | Apache-2.0 | OSM import; quick scenes |
| **PyBullet/Bullet** | RL, legged | Low | High | Community | zlib | Lightweight, great for RL loops |
| **Drake** | Controls/optimization | N/A (not a game engine) | High | Integrates | BSD-3 | Model-based design, verification |
| **Project Chrono** | Multiphysics, terrain | Low-Med | High | Integrates | BSD-3 | Deformable soils, vehicles |
| **RaiSim** | Dynamics/RL | Low | Very High | Integrates | Proprietary | Fast, deterministic |
| **Isaac Sim** | Perception + digital twins | **High (RTX)** | High (GPU) | **Yes (ROS 2)** | Mixed | USD/ROS2 workflows; Isaac Lab |
| **Unity + Robotics Hub** | Apps, UX, sim frontends | High | High | **Yes** | Proprietary | ROS-TCP; great for tools/HIL |
| **Unreal + Cesium** | Earth-scale twins | **Very High** | High | Community | Proprietary | Tiles not survey-grade |
| **AirSim** | UAV/UGV sim | High | Medium | Bridges | MIT | Unreal-based |
| **CARLA** | Autonomous driving | High | Medium | ROS bridge | MIT | Scenarios, OpenDRIVE, maps |
| **Habitat** | Embodied-AI | High (datasets) | **Very High** | Integrates | MIT | Navigation, HRI (3.0) |
| **OmniGibson / iGibson** | Home manipulation/nav | High | High | Integrates | Apache-2.0 | On Isaac Sim |
| **ManiSkill (SAPIEN)** | Manipulation RL | Med-High (GPU) | **Very High** | Integrates | BSD-3 | GPU-parallel capture |
| **PX4 SITL (jMAVSim)** | FCU bring-up/CI | Low | High | MAVLink/ROS | PX4 EULA | Community-maintained |
| **FlightGoggles** | UAV perception | **Very High (Unreal)** | Medium | ROS | BSD-3 | Photogrammetry scenes |
| **Flightmare** | UAV fast sim | Med (Unity) | **Very High** | Integrates | BSD-3 | Decoupled physics/render |
| **Genesis** | Research fast physics | Low-Med | **Extremely High** | Integrates | Apache-2.0 | Early; validate on your tasks |

---

### 8) Critical questions to ask before you pick

1) **What’s your truth source?** If you validate algorithms, where does “ground truth” geometry/pose come from? Cesium’s Photorealistic Tiles are gorgeous but *not survey-grade*. Use them for realism, not metrology.  
2) **Are you sim-limited by rendering or physics?** For RL throughput, MuJoCo/RaiSim/Genesis may beat photoreal engines by 10–100×; for perception, Isaac Sim/Unreal win.  
3) **Where do your logs live?** Unify **bag/ulog**/parquet so you can replay and query at scale (e.g., Arrow/Parquet + indices).  
4) **Do you need reproducibility?** Determinism is harder in complex, GPU-heavy stacks (timing, floating-point, seeds).  
5) **Is ROS 2 non-negotiable?** Favor modern Gazebo, Isaac Sim, Webots, Unity Robotics Hub.

---

### 9) A recommended **two-tier** stack for drone autonomy

**Tier A — Low-level, fast, CI-friendly (controls, estimation, mission logic):**  
- **PX4 SITL + jMAVSim** for smoke tests and fast loops; **Gazebo (modern)** or **Webots** for richer sensor kinematics while staying lightweight. Wire this into nightly CI with headless runs and hardware-in-the-loop tests on benches.  
- Log format: **ULog** (PX4) and **ROS 2 bag**. Build a single ingestion path that normalizes everything to **Arrow/Parquet** with time-aligned indices.

**Tier B — High-fidelity digital twin (perception, autonomy stress-tests, demo):**  
- **Isaac Sim** when you need physically-based sensors (RTX LiDAR/RGB-D), USD pipelines, and native ROS 2 graphs; great for indoor/industrial and for robot-learning with **Isaac Lab**.  
- **Unreal + Cesium for Unreal** when you need **Earth-scale** context or customer-facing demos. Use real terrain/tiles for realism + *your* surveyed meshes / photogrammetry for metric validation.  
- For UAV perception-only experiments, consider **FlightGoggles** (Unreal) or **Flightmare** (Unity) for speed/iteration.

**Why two tiers?** You’ll iterate 90% of the time on Tier A (cheap, deterministic, scale-out), then promote scenarios to Tier B to test perception + autonomy stacks under photoreal conditions and real geography—without paying the heavy GPU tax for every unit test.

---



### 10) Where the field is going (and what to watch)

- **OpenUSD & Omniverse pipelines** bring DCC-grade asset interoperability to robotics labs.  
- **Gaussian Splatting** enables fast capture/render of real places; expect Unreal/Unity integrations to mature for on-the-fly digital-twin updates. Use with caution for metrology until alignment improves.  
- **Ultra-fast physics (Genesis et al.)** will shrink RL training cycles; if claims hold for your tasks, you can replace big clusters with fewer GPUs—but still validate sim-to-real.

---

### 11) Practical starter kits (by goal)

- **Train UAV perception** → Isaac Sim (RTX sensors) or Unreal + FlightGoggles; render synthetic datasets with real camera models; add noise/latency.  
- **Evaluate planners/mission logic** → Gazebo/Webots with ROS 2; inject wind/GPS loss; bring PX4 SITL in the loop for mode transitions.  
- **Earth-scale demo/ops rehearsal** → Unreal + Cesium; fuse 3D Tiles with your own survey meshes; layer live ADS-B/weather feeds externally (not as truth).

---

### References (selected)

- MuJoCo — https://mujoco.org/ ; https://github.com/google-deepmind/mujoco  
- Gazebo (modern) — https://gazebosim.org/ ; migration notes from Gazebo Classic  
- Webots — https://cyberbotics.com/  
- PyBullet/Bullet — https://pybullet.org/wordpress/ ; https://github.com/bulletphysics/bullet3  
- CoppeliaSim — https://www.coppeliarobotics.com/  
- Drake — https://drake.mit.edu/  
- Project Chrono — https://projectchrono.org/  
- RaiSim — https://raisim.com/  
- NVIDIA Isaac Sim — https://docs.nvidia.com/isaac/isaac-sim/  
- Unity Robotics Hub — https://github.com/Unity-Technologies/Unity-Robotics-Hub  
- Unreal Engine — https://www.unrealengine.com/  
- Cesium for Unreal — https://cesium.com/platform/cesium-for-unreal/  
- Google Photorealistic 3D Tiles — https://developers.google.com/maps/documentation/photorealistic-3d-tiles  
- AirSim — https://github.com/microsoft/AirSim  
- CARLA — https://carla.org/  
- Habitat / Habitat-Sim — https://aihabitat.org/ ; arXiv:2306.11280 (Habitat 3.0)  
- OmniGibson — https://github.com/StanfordVL/OmniGibson  
- ManiSkill — https://github.com/haosulab/ManiSkill  
- FlightGoggles — https://flightgoggles.mit.edu/  
- Flightmare — https://github.com/uzh-rpg/flightmare  
- Genesis — https://github.com/Genesis-Embodied-AI/Genesis

---

### Image Sources 
- FlightGoggles — https://flightgoggles.mit.edu/ 
