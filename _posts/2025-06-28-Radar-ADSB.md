---
layout: post
title: Ground Radar and ADS-B for BVLOS Operations
date: 2025-06-28 8:00:00
description: Validating a ground radar's accuracy and its importance for BVLOS drone operations
tags: radar adsb drones air-traffic-control systems-engineering
categories: aerospace
thumbnail: assets/img/HiddenLevel.jpeg
published: true
---


{% include figure.liquid path="assets/img/HiddenLevel.jpeg" title="HiddenLevel-RadarAndADSB" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Overlay of radar detections (V shapes) and ADS-B tracks (aircraft shapes). Credits: Hidden Level</p>

As **unmanned aerial systems (UAS)** expand into **Beyond Visual Line of Sight (BVLOS)** missions, whether for drone deliveries, infrastructure inspection, or emergency response, ensuring **full airspace awareness** becomes mission-critical. One project I worked on tackled this challenge by comparing data from a ground-based radar with ADS-B (Automatic Dependent Surveillance–Broadcast), with the goal of assessing the radar’s tracking accuracy as part of a redundant airspace surveillance system, which would strengthen the situational awareness needed for autonomous UAS to complete operations safely.

---

### What I Worked On

The **high-level objective** of the project was to validate the **accuracy** of a **ground-based radar surveillance system** that integrates with other sensing layers—ADS-B and Remote ID—in order to deliver a full airspace picture to BVLOS drone operations. This system aims to ensure that autonomous drones can operate safely, even in environments with mixed or unreliable signal availability.

**At my level**, my focus was on **characterizing the accuracy and coverage of the radar**. Using Python, I processed large datasets from both the radar and nearby ADS-B receivers. Main tasks included:

- **Matching radar and ADS-B tracks** based on timestamp and 3D spatial proximity to evaluate the radar's positional accuracy.
- **Identifying radar detections** that had **no corresponding ADS-B track**—likely representing non-cooperative aircraft, drones, or possible noisy observations.
- **Determining which ADS-B tracks were not detected** by the radar, potentially due to occlusion, distance, or potential other factors.

Beyond these analyses, I also examined **what environmental and contextual factors** could influence the radar's performance. By correlating accuracy and detection gaps with **weather data, time of day, and geographical areas**, I aimed to understand the **limitations** and **potential optimization areas for deploying such radar systems**.

---

### Why We Need Ground Radar

While ADS-B is the dominant method for tracking large aircraft, it's not without failure modes:

- A transponder may malfunction.
- Certain aircraft—such as military or uncooperative planes—may intentionally fly without broadcasting.
- Even when functioning, ADS-B reception can be obstructed or interfered with.

Meanwhile, **drones are not allowed to broadcast ADS-B** under FAA Part 107 regulations. Instead, they must comply with **Remote ID** requirements—but Remote ID has its own challenges:

- The broadcast range is limited, and its effectiveness over long distances is currently a bit uncertain.
- Not all drones may comply or would need to comply, in the case of sub 250g drones.
- Remote ID, like ADS-B, depends on proper functioning hardware and line-of-sight.

Because of these limitations, **relying solely on ADS-B and Remote ID is not enough**. This is where **ground radar adds essential redundancy**. It doesn't depend on broadcast cooperation, and is capable of detecting flying objects within line-of-sight—whether cooperative or untracked. This redundancy is valuable not only for detecting **uncooperative aircraft**, but also for identifying **low-altitude drones and unknown aerial activity**—critical for safe integration of drones into shared airspace.


---

### Going Further: Towards Scalable and Resilient BVLOS Airspace Awareness

As drone adoption continues to grow—especially for autonomous and BVLOS operations—the airspace, particularly in and around urban areas, is expected to become more **congested and dynamic**. Ensuring the safety of these operations requires that every autonomous aircraft has access to real-time information about:

- **Crewed aircraft** via ADS-B
- **Nearby drones** via Remote ID
- **Non-cooperative or unexpected traffic** via radar

No single system is sufficient. A comprehensive solution will depend on **multi-layered sensing architectures**, where radar acts not as a backup but as a **critical redundant layer** alongside broadcast-based systems.

To support this, we’ll need:

- **More ground radars**—strategically positioned to minimize blind zones and maximize coverage
- **Optimized deployment locations**, accounting for terrain, urban clutter, and electromagnetic interference
- **Sensor fusion frameworks** that combine radar, ADS-B, and Remote ID into a **unified airspace picture**

However, a key challenge arises: BVLOS drones relying on ground-based surveillance must be able to **receive that data in real time**. If the communication link is lost, the drone loses its situational awareness, creating a safety risk.

This leads to an additional sensing level but also important tradeoff:

- **Ground-based awareness provides long-range, centralized detection**, but introduces communication dependency
- **Onboard sensors** (e.g. lightweight radar, cameras, acoustic or vision-based detect-and-avoid) offer **resilience**, but come with **cost, weight, and power tradeoffs**

For maximum safety, **future BVLOS drones should incorporate both**:

- **Live access to the shared surveillance layer** (ADS-B, Remote ID, ground radar)
- **Local onboard sensing** for additional awareness and collision avoidance 

This hybrid approach ensures that even if one layer fails—due to signal loss, sensor occlusion, or equipment malfunction—the drone still retains some level of autonomous awareness, enabling it to react and deconflict.




---
#### Image Credits:

[Hidden Level](https://www.hiddenlevel.com/press/hidden-level-awarded-u-s-air-force-phase-1-sttr-contract)

