---
layout: post
title: Comparing Ground Radar and ADS-B for Airspace Awareness
date: 2025-06-28 8:00:00
description: Validating a ground radar's accuracy and its importance for BVLOS drone operations
tags: radar adsb drones air-traffic-control systems-engineering
categories: aerospace
thumbnail: assets/img/radar-vs-adsb.jpg
published: false
---

One project I worked on consisted in a study between ADS-B data and radar data to evaluate the accuracy and reliability of a local air surveillance radar system. While the primary goal was to validate radar's tracking accuracy, this investigation provided deeper insights into airspace visibility, the limitations of existing systems, and the critical role of redundancy—especially in the context of beyond visual line of sight (BVLOS) drone operations.


#### Why Compare Radar and ADS-B?

Modern air traffic relies heavily on ADS-B (Automatic Dependent Surveillance–Broadcast), where aircraft actively broadcast their position, velocity, and other information. Ground stations can pick up this data and use it for traffic monitoring.

However, ADS-B isn't foolproof:

- Aircraft may turn off their transponders (intentionally or due to malfunction).
- Not all aerial objects are equipped with ADS-B (e.g., some military, older general aviation, birds, or drones).
- ADS-B data can be spoofed or dropped due to limited receiver coverage or antenna directionality.

Radar, on the other hand, does not rely on cooperation from the aircraft. For robust airspace awareness—especially in urban, cluttered, or GPS-denied environments—a well-calibrated radar is an essential backup.


#### Methodology
We had available data from:

- The ground-based radar system, recording detected objects with azimuth, range, and elevation.
- ADS-B receivers logging broadcasts from nearby aircraft.


Key analyses included:

- Matching radar tracks to ADS-B reports (based on timestamp and 3D proximity), and measuring positional differences between both systems.
- Identifying radar detections with no corresponding ADS-B (e.g., birds, drones, uncooperative aircraft).
- Determining ADS-B reports undetected by radar (could be due to obstruction, distance, clutter).



{% include figure.liquid path="assets/img/radar-vs-adsb-tracks.png" title="Radar and ADS-B track comparison" class="img-fluid rounded z-depth-1" zoomable=true %}

<p class="text-muted text-center mt-2">Overlay of radar detections (blue) and ADS-B tracks (red)</p>


#### Insights Beyond Accuracy

Beyond just validating accuracy, the data allowed for further analysis:

- Environmental factors (e.g., weather, fog) reduced radar reliability at certain times of day.
- Geographical occlusions creating weak zones in radar coverage.
- Trustworthiness of ADS-B (temporal gaps and data on N)
- Temporal gaps in ADS-B data pointed to issues with line-of-sight reception or aircraft exiting service areas.


#### Why This Matters for Drones

As drone operations grow, especially BVLOS (Beyond Visual Line of Sight) missions for logistics, inspection, or emergency response, airspace awareness becomes more critical than ever.

ADS-B is not enough. While current regulations require drones over .55 lbs to broadcast Remote ID signals, these are currently limited in range and power, especially for small UAVs. The FAA Remote ID rule (Part 89) mandates that most drones transmit location and identity, but in practice, reception distance is often just a few hundred meters.
Radar adds a layer of safety. Drones need situational awareness of other aircraft, including those not broadcasting, and radar provides that redundancy.


In multi-drone environments or when drones coexist with general aviation aircraft, a fusion of radar + ADS-B is vital for real-time deconfliction and collision avoidance.

Even large crewed aircraft can occasionally fly without functional ADS-B, due to equipment failure or regulatory exceptions. Without radar, such traffic would be invisible to both drones and air traffic control relying solely on ADS-B.


#### Systems Engineering Takeaways

- Redundancy is not optional: A robust airspace monitoring system must not rely on a single sensing modality.
- Environment modeling is essential: Each radar blind spots must be mapped and mitigated with multiple radars and using multi-sensor fusion.
- Labeling uncorrelated tracks matters: Identifying radar detections with no ADS-B match helps characterize potential non-cooperative targets like rogue drones, aircraft, or sensor artifacts.
- Future UTM (Unmanned Traffic Management) systems must integrate multiple tracking technologies, both active (active radars) and passive (receiving ADS-B, Remote ID, passive radars, camera-based tracking technologies).
