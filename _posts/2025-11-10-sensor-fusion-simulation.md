---
layout: post
title: Test Sensor Fusion in Simulation
date: 2025-11-10 10:00:00
description: Test sensor fusion methods in a 2D environment.
tags: drones robotics autonomy localization gps
categories: robotics
thumbnail: assets/video/sensor-fusion-sim-waypoints2.mp4
published: true
---

## 2D Simulator Overview


{% include video.liquid path="assets/video/sensor-fusion-sim-controls2.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
<p class="text-muted text-center mt-2">Manual Controls via two joysticks. Left joystick controls translatioinal movement (forward, backward, left, right, in the drone frame) and right joystick controls yaw (moving joystick to left leads to drone rotating counter-clockwise).</p>



{% include video.liquid path="assets/video/sensor-fusion-sim-waypoints2.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
<p class="text-muted text-center mt-2">Autonomous waypoint navigation based on the drone's estimated position.</p>


## Sensor Modeling and Impact of Faults in Sensors on Mission

{% include video.liquid path="assets/video/sensor-fusion-sensors2.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
<p class="text-muted text-center mt-2">Values ouptutted by each sensor are modeled as a Gaussian distribution with a certain spread (standard deviation) and offset from the ground truth. Each sensor also has its own update frequency, and has the option to be in "normal" status, "off", "faulty" (high standard deviation) or "spoofed" (low standard deviation but fabricated offsets). In this video, gps and aerial geolocalization are fused to get a position estimate, and we assume a magnetometer is providing accurate heading. When GPS is turned off, the drone relies fully on aerial geolocalization (set to provide updates less often). When GPS is faulty or spoofed, the fused pose estimate is heavily impacted, resulting in it reaching waypoints it think it did, when in reality it did not (need to compare its estimated position with its ground truth here). Ideally, a more robust sensor fusion algorithm should be implemented, which allows to detect and recognize faulty or unexpected sensor measurements, and re-weigh the importance of each of its sensor.</p>