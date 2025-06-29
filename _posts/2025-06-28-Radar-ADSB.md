---
layout: post
title: Ground Radar and ADS-B for BVLOS operations
date: 2025-06-28 8:00:00
description: Validating a ground radar's accuracy and its importance for BVLOS drone operations
tags: radar adsb drones air-traffic-control systems-engineering
categories: aerospace
thumbnail: assets/img/HiddenLevel.jpeg
published: true
---


{% include figure.liquid path="assets/img/HiddenLevel.jpeg" title="HiddenLevel-RadarAndADSB" class="img-fluid rounded z-depth-1" zoomable=true %}

<p class="text-muted text-center mt-2">Overlay of radar detections (V shapes) and ADS-B tracks (aircraft shapes). Credits: Hidden Level</p>


As **uncrewed aerial systems (UAS)** expand into **Beyond Visual Line of Sight (BVLOS)** missions, whether for logistics, infrastructure inspection, or emergency response, ensuring **full airspace awareness** becomes mission-critical. One project I worked on tackled this challenge by comparing data from a ground-based radar with ADS-B (Automatic Dependent Surveillance–Broadcast), with the goal of assessing the radar’s tracking accuracy as part of a redundant airspace surveillance system, which would strengthen the situational awareness needed for autonomous UAS to complete operations safely.


#### Why Redundancy Matters for UAS Airspace Awareness
ADS-B is currently the primary mechanism for tracking aircraft, but it’s not infallible. A few key limitations:

- Aircraft may not be broadcasting ADS-B (e.g., military or uncooperative aircraft)
- Transponders may malfunction
- Signal reception may be affected by interference, terrain, or obstructions
- Small drones are not allowed to broadcast ADS-B

And while Remote ID is now required for most drones under FAA rules (Part 89), it's not a perfect substitute:

- Possibility of uncooperativeness or broadcast malfunction like with ADS-B
- Range limitations may reduce its effectiveness for wide-area situational awareness

For these reasons, relying solely on ADS-B or Remote ID is insufficient—especially in regions of increasingly congested airspace with both manned and unmanned aircraft. This is where ground radar adds critical redundancy. Ground radar doesn’t rely on cooperation and has long been used in traditional air traffic control. But as drone operations move into urban environments, we’ll need distributed radar nodes or additional sensors (cameras, passive RF, acoustic arrays) to extend coverage.


#### Short Project Overview: Radar + ADS-B Comparison
We had available data from:

- The ground-based radar system, recording detected objects with azimuth, range, and elevation.
- ADS-B receivers logging broadcasts from nearby aircraft.


Key analyses included:

- Matching radar tracks to ADS-B reports (based on timestamp and 3D proximity), and measuring positional differences between both systems.
- Identifying radar detections with no corresponding ADS-B (e.g., birds, drones, uncooperative aircraft).
- Determining ADS-B reports undetected by radar (could be due to obstruction, distance, clutter).


#### Insights Beyond Accuracy

Beyond just validating accuracy, the data allowed for further analysis:

- Environmental factors (e.g., weather, fog) reduced radar reliability at certain times of day.
- Geographical occlusions creating weak zones in radar coverage.
- Trustworthiness of ADS-B (temporal gaps and data on N)
- Temporal gaps in ADS-B data pointed to issues with line-of-sight reception or aircraft exiting service areas.


#### Going Beyond this Project: Implications for Future Drone Operations


As drone operations grow, especially BVLOS (Beyond Visual Line of Sight) and fully autonomous missions for logistics, inspection, or emergency response, airspace awareness becomes more critical than ever.

ADS-B and Remote ID both have limitationsRadar adds a layer of safety. Drones need situational awareness of other aircraft, including those not broadcasting, and radar provides that redundancy.

But there's a caveat: relying solely on a ground-based system introduces its own risk—the drone becomes dependent on real-time data transmission from the ground. A loss of comms means a loss of situational awareness. The ideal long-term solution is a hybrid model:

- Onboard ADS-B & Remote ID receivers for general awareness and planning ahead
- Ground-based radar as a redundant layer
- Ideally also onboard sensors for proximity detect-and-avoid (vision, acoustic, radar). This does however mean additional weight, cost, and reduced flight time

#### Systems Engineering Takeaways

- Redundancy is not optional: A robust airspace monitoring system must not rely on a single sensing modality.
- Labeling uncorrelated tracks matters: Identifying radar detections with no ADS-B match helps characterize potential non-cooperative targets like rogue drones, aircraft, or sensor artifacts.
- Environment mapping is a must: Understanding radar blind spots due to buildings or terrain is key for smart node placement.
- Ground-based systems can support autonomy, but introduce their own dependencies. Full autonomy should require onboard, real-time sensing.
- Future UTM (Unmanned Traffic Management) systems should integrate multiple tracking technologies, both active (active radars) and passive (receiving ADS-B, Remote ID, passive radars, camera-based tracking technologies).


Future UTM systems should fuse radar, ADS-B, Remote ID, and onboard sensors for real-time deconfliction.

