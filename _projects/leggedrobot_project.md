---
layout: page
title: Legged Robotics
description: Teaching biped and quadruped robots to walk 
img: assets/img/dog.gif
importance: 6
category: School Projects
---

<div class="row justify-content-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/atlas.gif" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

The goal of this project was to **study and implement state-of-the-art locomotion algorithms for legged robots**, focusing on both **bipedal** and **quadrupedal** systems. Our work explored approaches ranging from **analytical trajectory planning** to **bio-inspired oscillators** and **deep reinforcement learning**, with the aim of understanding and improving how robots achieve stable and adaptive movement.  

## Bipedal Locomotion  

For bipeds, we implemented locomotion based on the **Divergent Component of Motion (DCM)** framework:  
- **DCM Planning** – generating stable Center of Mass (CoM) trajectories.  
- **Foot Trajectory Planning** – ensuring dynamic balance with fixed-step positioning.  
- **Inverse Kinematics** – mapping trajectories into executable joint motions.  

This resulted in a robust controller capable of maintaining balance and walking smoothly on flat terrain.  

## Quadrupedal Locomotion  

For quadrupeds, we combined **bio-inspired control** with **learning-based methods**:  
- **Central Pattern Generators (CPGs)** – oscillator-based gait generation (walk, trot, pace, bound).  
- **PD Control** – Cartesian and joint-space foot placement.  
- **Deep Reinforcement Learning (DRL)** – training policies with PPO and SAC to adapt locomotion across challenging terrains.  

These approaches allowed us to explore the trade-offs between **model-based control** and **data-driven learning**, while building a foundation for more advanced legged robotics research.  


Github <a href="https://github.com/nlugon/legged-robotics">here</a>.

Youtube <a href="https://www.youtube.com/watch?v=mAbwYRhE2rQ">here</a>.