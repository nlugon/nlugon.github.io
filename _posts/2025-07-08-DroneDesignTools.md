---
layout: post
title: Optimized Drone Design
date: 2025-07-08 10:00:00
description: Design a quadcopter given a desired minimum payload mass and minimum flight time
tags: drones tools flight-time battery
categories: drone-tools
thumbnail: assets/img/drone-design/hovertime-vs-capacity.png
published: true
---


{% include figure.liquid path="assets/img/drone-design/hovertime-vs-capacity.png" title="Optimized Quadcopter Design" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Hover Time vs Battery Capacity for different battery types and number of cells. This plot was obtained from applying fairly simplistic physics to large combinations of different propellers, motors and battery specifications (with fairly realistic catalogs of propellers, motors and batteries being synthetically generated with AI).</p>

## 1) Goal

**Design a quadcopter** that satisfies:
- **Payload** ≥ target (g)
- **Flight time** ≥ target (min)
- **Total mass** ≤ limit (g)
- **Total cost** ≤ budget (USD)

**Output:** Feasible configurations (propeller, motor, battery) with hover/max/burst metrics & plots. Among these configurations, find an optimal solution that also takes into account other factors (agility, thrust-to-weight ratio, average battery discharge rate, ...)

---

## 2) Approach

### 2.1 Simplifying problem
- Each component affects the other components -> not so straightforward to just come up with an analytical formula. So one idea would be to simulate the performance of a large amount of combination of different components, and do some data analysis to figure out what combinations best meet our requirements.
- Main components we consider for design: **propellers**, **motors**, **battery**.
- Pretty much ignore everything else (can set an approximate mass for frame, wiring, avionics, ... based on the propeller size).
- Assume **4 rotors** (quadcopter).

### 2.2 Datasets (synthetic but realistic)
Use AI to generate plausible catalogs of:

- **Propellers**: diameter (5–33″), pitch, blades, mass, inertia, **thrust vs RPM** curve, unit cost.
- **Motors**: size (e.g., 1404–7215), KV, Kt, mass, nominal/max currents, voltage range, acceptable prop diameter range, cost.
- **Batteries**: capacity (mAh), cells (S), C‑rating, chemistry (LiPo/Li‑ion), mass, cost.

Files are JSON: `propellers.json`, `motors.json`, `batteries.json`.

### 2.3 Algorithm (simplified): Find combinations that satisfy requirements
For each (prop, motor, battery) combination:

TODO



---

## 3) Results



TODO

{% include figure.liquid path="assets/img/drone-design/hovertime-vs-capacity.png" title="Optimized Quadcopter Design" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Hover Time vs Battery Capacity for different battery types and number of cells. Note that this plot relies on multiple simplified assumptions and also relies on specs for props/motors/batteries that were synthetically generated (prompted to be as realistic as possible, but may can differ to current off-the-shelf parts).</p>

{% include figure.liquid path="assets/img/drone-design/twr-vs-capacity.png" title="Optimized Quadcopter Design" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Thrust-to-Weight ratio vs Battery Capacity for different battery types and number of cells.</p>


### Discussion

#### Aspects that look good/make sense:
- Liion batteries lead to longer flight times that LiPo (expected)
- Higher cell-count seems to lead to longer flight times. Note that higher cell-count should also mean more heavy drone, which would then decrease the flight time, 
- A too large battery capacity actually ends up in decreased flight time compared to a smaller capacity battery (too much weight which the current prop/motor/battery configuration may no longer be good for)



---

## 4) Next steps


### Improvements needed for current approach:
- Replace synthetic entries with actual vendor data and also keep track of links and part availability.
- Add more complexity in the physics to take into account non-linear battery drainage, motor I₀, winding resistance Rₘ, ESC efficiency, wiring drops, ... 

### Broaden application
- **Multicopters**: generalize rotor count (tri, hexa, octo), consider having two propellers above each other (the turbulent air on the propeller below will lead to less generated thrust)
- **VTOL / Fixed‑wing**: add aerodynamic power & propulsive efficiency vs airspeed.


---