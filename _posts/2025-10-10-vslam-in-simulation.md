---
layout: post
title: "Testing VSLAM in simulation"
date: 2025-10-10 23:34:01
description: "As described in title."
tags: vslam slam simulation isaac-sim usd photogrammetry fisheye apriltag imu
categories: robotics
thumbnail: assets/img/vslam-sim-thumb.jpg
published: true
---

# Testing VSLAM in simulation

So I got a **Jetson Orin Nano** and set it up with a camera such that I can run VSLAM algorithms with it (the idea: map the inside of a room, and then know at all times where it is located inside of the room). I did however end up spending more time than anticipated simply getting the Jetson setup and interfacing with the camera, with in addition 3d printing a case and camera mount to keep both parts fairly secure and fixed to one another. One complete unified guide (if there are multiple options to set up, I'll end up spending too much extra time researching what option to choose) or set of instructions would have helped speed things up (I do have a maybe not so good tendancy of following multiple video youtube guides rather than just the main written guide)(so I'll share a guide in a seperate post)


and also wanted the camera to be fairly secure via a 3D printed mount (which had to be designed and 3d printed)



(going through the setup guides and using a bit of chatgpt)(although using ). One thing that would have helped speed things up was to have a trusted and well‑organized guide/tutorial/documentation (when using chatgpt, it actually did a fairly good job outlining all of the steps, but I still had to check its sources and understand what I was actually doing). But still, unless I got a power bank I'd have a power cable sticking o



also wanted the camera to be fairly well secured to the Jetson so designing and 3D printing a case and camera mount also added time. And then unless I got a power bank for it I would need to 




But speeding things up even more would be to **not deal with the hardware setup at all** (+saves money), and just test everything **in simulation**.

---

## So, how to test everything in simulation?

**First step:** figure out what simulation platform I need.

> *(very compact list, not exhaustive)*

- **Isaac Sim (Omniverse)** — realistic visuals, robotics‑oriented, USD‑native, good sensor models.  
- **Gazebo (Gazebo Sim)** — open source, ROS 2 first‑class, lots of plugins.  
- **Webots** — quick start, stable for education/research, good docs.  
- **Unreal + AirSim** — very photoreal, good for drones; more setup.

I ended up going for **Isaac Sim**, because it looks quite realistic, it’s purposely made for robotics, and has a lot of momentum going for it (and my desktop also has an NVIDIA GPU). *(Well technically I originally bought my desktop with Gazebo, Unreal Engine and NVIDIA Isaac Sim in mind.)*

---

## Modeling the world (my room)

Next step: how to model the world (in my case the room) inside of this simulation? Luckily, tasks involving building **digital twins** are becoming more common (e.g. **photogrammetry** and **gaussian splatting**). If you're working in a small room, apps such as **Polycam** or **Scaniverse** should do the trick.

In my case, I was actually scanning the inside of an **old 747 aircraft hangar**, and that did not work. What did work fairly well was: film the whole inside of the hangar (following some general photogrammetry tips—e.g., avoid having multiple frames taken with a different rotation but from the same location), then get a 3D model out of it using **RealityCapture/RealityScan**. If you're working outside, you'd probably want **gaussian splatting** in order to see the sky *(but then are "objects" like the floor given a mesh, and have the ability to have collisions defined? Short answer: splats are not meshes; you usually add a simple proxy mesh/plane for collisions).*

So I did that, and here is what the scan looked like. Given the metal roof beams are pretty narrow and thus challenging to accurately capture with photogrammetry, I did some cleaning up in **Blender**.

---

## Exporting as USD → dropping into Isaac

Next, I exported this as a **USD** file and literally dragged and dropped it into Isaac Sim. Pretty simple.

> **USD, very quickly:** **Universal Scene Description** (from Pixar) is a scene‑graph format that supports layering/composition and large scenes. Compared to **OBJ** (static mesh) it keeps richer structure and metadata; compared to **FBX**, it’s more open and pipeline‑friendly. Isaac/Omniverse speaks USD natively.

---

## My “robot” for now = a camera

Now that I have my world, it's time to integrate my robot inside (whatever my robot may be). My first goal here is **perception / computer vision**, so I’m **not** interested in actuation/motion at the moment (so I don’t need to worry about gravity or collisions yet). I’ll basically start by saying that **my robot is just a camera**. (Technically that makes it *not* a robot if it has no way to actuate—more a sensor + processing unit right now.)

I liked the idea of using a **fisheye** lens. Yes, distortion is annoying, but it captures a lot more of the environment. In theory two 220° FOV cameras can cover basically everything.

**How I set this up (simple):**

1. Import the USD room.  
2. Add a **Camera** prim; set pose.  
3. Pick projection/intrinsics (pinhole first; fisheye later).  
4. Create a **render product** and stream frames to my VSLAM process.  
5. If doing fisheye: keep a consistent calibration model in sim and in my code (OpenCV fisheye / Kannala–Brandt or NVIDIA VPI).

---

## Make localization easier (for now)

As part of this sensing/perception project, there may be a lot of challenges in being able to reliably estimate position inside of the space, while targeting ~meter‑level precision (GPS‑like). For this application, I don’t need it to generalize to other rooms or outdoors **right now**. I just want it to work well inside of this **particular hangar**.

So I can make my life easier and place **markers** or **easy features** in distinct areas of the **hangar**. AprilTags on a few columns, a couple of high‑contrast panels, stuff like that.

---

## One problem: pitch/roll looks like translation

One problem I noticed: when pitching or rolling the camera (realistic if it’s on a drone), from the camera’s perspective it looks like a **translation** (forward/sideways), which is not what is happening.

So how to solve this? A few small, practical things:

- Add an **IMU** and use **Visual‑Inertial** SLAM (e.g., ORB‑SLAM3 VIO).  
- Keep **AprilTags** or other hard references for occasional absolute corrections.  
- Capture with some **true parallax** (not only rotation), or reduce aggressive roll/pitch during mapping.

---

## Next

Next: mount this camera onboard an actual **drone**. This is where I’ll start to use the simulator’s **physics** (gravity, collisions), add an **IMU** with realistic noise, and compare VSLAM drift against known marker poses in the hangar.

---

## References

- Isaac Sim — Camera sensors: <https://docs.isaacsim.omniverse.nvidia.com/4.5.0/sensors/isaacsim_sensors_camera.html>  
- OpenUSD docs: <https://openusd.org/docs/>  
- OpenCV fisheye model: <https://docs.opencv.org/4.x/db/d58/group__calib3d__fisheye.html>  
- AprilTag (official repo): <https://github.com/AprilRobotics/apriltag>  
- ORB‑SLAM3 (paper/code): <https://arxiv.org/abs/2007.11898> • <https://github.com/UZ-SLAMLab/ORB_SLAM3>