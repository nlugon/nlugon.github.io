---
layout: post
title: "Designing a $30 Versatile and Scalable Robot"
date: 2025-10-22 22:23:21
description: "A small differential-drive robot built around ESP32-CAM, TT motors, and an L298N—cheap, opening doors to simple onboard autonomy, phone connectivity with UI development, and more."
tags: robotics diy esp32-cam swarm
categories: robotics
thumbnail: assets/img/robot-tt-esp32cam.jpg
published: true
---

> **TL;DR** — I wanted an extremely cheap, easy-to-build robot to prototype CV + autonomy ideas. I couldn’t quite find what I had in mind, so I designed a tiny differential-drive platform that uses cheap and readily available components: **2× TT gear motors**, an **L298N**, an **ESP32-CAM** (like an Arduino, but has an onboard camera and can connect to WiFi, a phone or other robots), and **4×AA** batteries. It’s built with the idea of open, hackable, swarm-friendly, and upgradable to a “heavier” build (**Jetson Orin Nano + RPLIDAR**) when needed.

---

## Images

*(Insert your images once you have them.)*

{% include figure.liquid path="assets/img/robot-final-topdown.jpg" title="Figure 1 – Final assembled robot, top-down" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Figure 1 – final assembled robot, top-down.</p>

{% include figure.liquid path="assets/img/robot-exploded-cad.jpg" title="Figure 2 – Exploded CAD with all components and wiring" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Figure 2 – exploded CAD with all components and wiring.</p>

---

## 1) Current Gap (from my fairly-quick research)

I needed a throwaway-cheap platform to try ideas fast: onboard vision with the potential for swarm interaction or SLAM capabilities later, without spending too much time or money. I found similar things, but nothing that quite matched my constraints: **under $30 per unit**, **order today/assemble this weekend**, **Wi‑Fi camera on board**, ready to **scale to half a dozen units** for swarm experiments.

Here are a few representative I found:

- **ESP32-CAM robot tutorials/kits** — great inspiration, proves feasibility; typically combine ESP32-CAM + L298N and a small chassis.  
  *Examples:* Random Nerd Tutorials (+2), DroneBot Workshop (+2)

- **2WD “TT motor” car chassis kits** — readily available across vendors in many flavors; cheap and fast to ship.  
  *Examples:* Cytron Technologies (+2), Walmart.com (+2)

- **TT motors in bulk** — packs of 10 are widely sold; pricing varies by vendor but is often economical for swarms.  
  *Examples:* Amazon (+3), Amazon (+3), Amazon (+3)

These gave me confidence the parts are commodity — but I still wanted a single, minimal **BOM** and a **clean, repeatable build** that’s easy to scale.

---

## 2) Design goals

- As cheap as possible 
- No exotic parts, only readily available (ideally next day delivery from common marketplaces) 
- 3D printing and assembly in minimal time (avoid lots of parts that need to be fastned with screws).  
- Real-time on‑board vision (**ESP32-CAM** streams video and does simple CV).  
- Swarm‑ready (Wi‑Fi control, cheap and bulk‑buy components).  
- Can be upgraded to more advanced compute/sensors (e.g., **Jetson Orin Nano + RPLIDAR**).

---

## 3) Final architecture

- **Drive:** Differential, 2× TT motors with wheels (cheap, ubiquitous).  
- **Motor driver:** L298N dual H‑bridge module for two DC motors (5–35 V input; ~2 A/bridge).  
  *References:* Components101 (+2), Smart Prototyping (+2)  
- **Brain + camera:** ESP32‑CAM (handles video streaming and PWM/GPIO for motor control). Typical draw ~**180–310 mA** depending on LED/stream; peaks can be higher—design your power margins accordingly.  
  *References:* docs.sunfounder.com (+2), Core Electronics Forum (+2)  
- **Power:** **4×AA** (alkaline or rechargeable NiMH). Most basic. If you already have LiPo or Lion batteries (pack, 18650, 21700), can use those
  *References:* Husarion (+2), Grepow (+2)

**Why L298N?** It’s everywhere, cheap, and beginner‑proof. Downsides: it’s an older bipolar design with noticeable voltage drop and heat versus modern MOSFET drivers. If you want leaner and more efficient, consider **L9110S, TB6612, MX1508, or DRV8833**—they’re widely used for small TT motors.  
*References:* elecrow.com (+2), RobotShop Community (+2)

---

## 4) Bill of Materials (BOM)

| Item | Example / Notes | Qty | Est. unit cost* |
|---|---|:---:|:---:|
| TT motor + wheel | 10-packs widely sold; buy 2 per robot *(Amazon +1)* | 2 | $1–$2 ea (bulk) |
| L298N motor driver | Off-the-shelf module *(Smart Prototyping)* | 1 | $3–$6 |
| ESP32‑CAM | AI‑Thinker or equivalent *(Last Minute Engineers)* | 1 | $6–$12 |
| 4×AA holder + AAs | Alkaline or NiMH rechargeables *(Husarion)* | 1 | $3–$8 |
| Chassis | 3D‑printed plate or generic 2WD kit base *(Cytron Technologies)* | 1 | $0 (print)–$10 |
| Odds & ends | M3 hardware, wires, header leads | — | $2–$4 |

<small>*Pricing varies by vendor/region; shown to convey order‑of‑magnitude based on common listings.*</small>

**Target cost (single unit):** $20–$30 if you already have basic tools and can print/laser‑cut the plate; less per unit when building a small swarm (motors in **10‑packs** help a lot). *(Amazon)*

---

## 5) Reference designs I learned from

- **Random Nerd Tutorials** – ESP32‑CAM Car (web server + streaming + control). *(Random Nerd Tutorials)*  
- **DroneBot Workshop** – ESP32‑CAM Robot Car (end‑to‑end guide and power notes). *(DroneBot Workshop)*  
- **Keyestudio ESP32‑CAM 4WD** (example of pairing ESP32‑CAM + L298N). *(docs.keyestudio.com)*  
- **Generic 2WD TT chassis kits** (fastest way to a rolling base). *(Cytron Technologies)*

These informed my choices and helped me avoid dead ends.

---

## 6) Key design decisions (and the trade-offs)

### 6.1 Motor driver: L298N vs “modern” modules
- **L298N:** bulletproof availability; dual H‑bridge; up to ~2 A/bridge; handles 5–35 V. **Cons:** larger voltage drop → less torque at the wheel for a given battery, more waste heat. *(Components101 +1)*  
- **L9110S / TB6612 / MX1508 / DRV8833:** more efficient for low‑voltage TT motors; smaller, cooler, better battery life — but sometimes a touch less “forgiving” for absolute beginners. *(elecrow.com +1)*

**My call:** ship the baseline with **L298N** for ubiquity, and document drop‑in alternatives.

### 6.2 Battery: AA (alkaline/NiMH) vs Li‑ion/LiPo
- **AA (alkaline or NiMH):** dead‑simple logistics, easy to source globally, and charging NiMH is comparatively forgiving. Great for classroom and hobby contexts where safety and simplicity matter. *(Husarion +1)*  
- **Li‑ion/LiPo:** higher energy density, better cycle life and charge retention, but require proper chargers and safety practices; excellent when you need more runtime/power. **LiFePO₄** is a safer lithium chemistry worth considering. *(tritekbattery.com +2, EcoFlow +2)*

**My call:** default to **AA** for the “hassle‑free” build; document a **Li‑ion/LiFePO₄** option for advanced users.

### 6.3 Compute: ESP32‑CAM for “one‑chip vision + control”
**ESP32‑CAM** integrates Wi‑Fi + camera; it can stream MJPEG over a web page while also toggling GPIOs for motor control. Budget ~**180–310 mA @ 5 V** during streaming with flash variations; allow margin for peaks (some users report ~300–500 mA spikes). **Power it from the 5 V pin** rather than 3.3 V for stability. *(Last Minute Engineers +3; docs.sunfounder.com +3; Core Electronics Forum +3)*

---

## 7) Electrical + firmware overview

**Wiring (baseline):**
- ESP32‑CAM **5V** → battery rail (through switch); **GND** common with L298N.  
- ESP32‑CAM two **GPIOs per motor** (e.g., GPIO12/13 → IN1/IN2; GPIO14/15 → IN3/IN4).  
- L298N **ENA/ENB** either tied **HIGH** or **PWM’d** from ESP32‑CAM timer channels.  
- Motors to **L298N OUT1/OUT2** and **OUT3/OUT4**.  
- Optional **5 V buck** (if you go Li‑ion): feed L298N + ESP32‑CAM stable 5 V.

**Firmware sketch (high level):**
- Start **AP** or join **Wi‑Fi**; expose a small web UI with live video.  
- Control page: forward/back/left/right/stop + **speed sliders (PWM)**.  
- Add a **debug overlay** (FPS, battery, RSSI).  
- **CV mode:** HSV thresholding or **AprilTags** → send motor commands accordingly.

If you want to jump-start, the ESP32‑CAM car articles provide solid scaffolding that you can adapt. *(Random Nerd Tutorials +1)*

---

## 8) Build + test plan

**Mechanical (60–90 min):**
- Print/laser the plate; mount TT motors and caster/skid.  
- Standoff the ESP32‑CAM and L298N on the top deck for airflow and Wi‑Fi.  
- Zip‑tie cable relief; label motor polarity for consistency across a swarm.

**Electrical (45–60 min):**
- Wire battery → switch → 5 V rail; common grounds.  
- ESP32‑CAM GPIOs → L298N inputs; verify ENA/ENB PWM.  
- Bring‑up test on bench: stream video; jog motors at low duty.

**Software (30–60 min):**
- Flash web‑UI + streaming; verify control latency.  
- Add **fail‑safes** (stop on link loss; watchdog).  
- Optional: baseline CV (color blob or tag detection) to demo autonomy.

**Acceptance checks:**
- Video ≥ **10–15 FPS** at QVGA; latency **< 300 ms** on local Wi‑Fi.  
- Robot drives straight **±5° over 1 m** after calibration.  
- **Runtime goal:** **20–40 minutes** on AA NiMH depending on duty cycle (expect less with Alkaline at high loads).

---

## 9) Swarm-readiness

- **Cost scaling:** motors in 10‑packs and bulk standoffs/wire reduce per‑unit cost. *(Amazon)*  
- **Identity & control:** per‑robot hostname/SSID; WebSocket or UDP broadcast control; start with simple “multi‑teleop” page.  
- **Synchronization:** assign time slots for motion; add AprilTags or colored beacons for global pose if desired.  
- **Fleet safety:** idle timeout, geo‑fences (grid cells on floor), and speed caps.

---

## 10) Upgrade path: when you need “real” sensors/compute

The same chassis pattern can be scaled up (wider plate, stiffer mounts) to carry **Jetson Orin Nano** and an **RPLIDAR** for mapping/VO. Swapping the **L298N** for a **TB6612** or larger driver, adding **encoders**, and bumping to **Li‑ion/LiFePO₄** will give you the headroom to run real‑time SLAM stacks.

*(For inspiration on higher‑end, off‑the‑shelf kits that bundle Orin Nano with a chassis/sensors, vendors exist — but they’re orders of magnitude more expensive, which is exactly the niche this project avoids.)* *(Oz Robotics)*

---

## 11) CAD + repo

- **CAD:** parametric plate (**DXF + STEP**) with hole patterns for TT motors, L298N, ESP32‑CAM standoffs, and battery holder.  
- **Firmware:** minimal web‑UI; motor API; optional CV module.  
- **Docs:** wiring diagram, pin map, and a one‑page bring‑up checklist.

**Images**  
Figure 3 – wiring diagram (ESP32‑CAM ↔ L298N ↔ TT motors).  
Figure 4 – dimensioned top plate (DXF/STEP).

---

## 12) Results

With a single afternoon build, I can:
- **Teleop via browser + live video.**  
- **Run simple HSV thresholding** on‑board and do reactive behaviors.  
- **Duplicate 5–10 robots cheaply** for group behaviors.

Thermals are manageable; the **L298N** gets warm at sustained high duty due to voltage drop (expected for this class of driver). If endurance matters, switch to a modern driver to reclaim efficiency. *(RobotShop Community)*

---

## 13) Battery note (why I chose AA for the baseline)

I defaulted to **AA** for the beginner/hobbyist build: cheap, globally available, and straightforward charging/swap logistics—great for classrooms and casual use. **NiMH AAs** are especially forgiving to charge compared to **Li‑ion/LiPo**, which require stricter chargers/procedures. If you need more runtime or punch, step up to **Li‑ion/LiFePO₄** with a proper buck to 5 V and clear safety practices. *(EcoFlow +3; Husarion +3; Grepow +3)*

*(If you disagree and prefer Li‑ion from day one: totally valid for experienced builders. I’ll include a **LiFePO₄** variant in the repo with a recommended buck and charge‑safety checklist.)* *(Kamada Power)*

---

## 14) What this design is (and isn’t)

**Pros**
- Ultra‑low cost, fast to source + assemble.  
- All‑in‑one **ESP32‑CAM** simplifies vision + control. *(Random Nerd Tutorials)*  
- Great for swarms—buy parts in bulk and duplicate builds. *(Amazon)*  
- Teachable—students can build and understand the whole stack.

**Cons**
- Limited torque + control fidelity with **TT motors** (no encoders by default).  
- **L298N** efficiency isn’t stellar; a modern driver extends runtime. *(RobotShop Community)*  
- Wi‑Fi latency and camera bandwidth cap your autonomy loop rates.

**When another platform may be better**
- If you need precise **odometry**, choose gearmotors with **encoders** or a kit that includes them.  
- If you need **long runtime/heavier payloads**, start with **Li‑ion/LiFePO₄** and a more efficient driver.  
- If you need **ROS2 + 2D lidar mapping**, jump straight to a larger base that can carry **Jetson + RPLIDAR** cleanly.

---

## 15) What’s next

- Publish the open **CAD**, **wiring**, and **starter firmware**.  
- Add **AprilTag** support and a **calibration page** (motor trim, camera exposure, HSV presets).  
- Provide a **modern‑driver** variant (**TB6612/L9110S**) BOM + wiring. *(elecrow.com)*  
- Release a **swarm control page** (multi‑robot telemetry + teleop).

---

## References & further reading

- **TT motors (bulk examples).** Amazon (+2), Amazon (+2)  
- **L298N specs/datasheet.** Components101 (+1)  
- **ESP32‑CAM robot guides.** Random Nerd Tutorials (+1)  
- **ESP32‑CAM power behavior and best‑practice powering.** docs.sunfounder.com (+2); Core Electronics Forum (+2)  
- **Modern lightweight drivers (L9110S, etc.).** elecrow.com (+1)  
- **Battery selection for small robots.** Husarion (+1)

---



---

> **TL;DR** — I wanted an extremely cheap, easy-to-build robot to prototype CV + autonomy ideas. I couldn’t quite find what I had in mind, so I designed a tiny differential-drive platform that uses commodity parts: **2× TT gear motors**, an **L298N** (or leaner alternative), an **ESP32-CAM** (does vision *and* motor control), and **4×AA** batteries. It’s open, hackable, swarm-friendly, and upgradable to a “heavier” build (**Jetson Orin Nano + RPLIDAR**) when needed.

---

## Images

*(Insert your images once you have them.)*

{% include figure.liquid path="assets/img/robot-final-topdown.jpg" title="Figure 1 – Final assembled robot, top-down" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Figure 1 – final assembled robot, top-down.</p>

{% include figure.liquid path="assets/img/robot-exploded-cad.jpg" title="Figure 2 – Exploded CAD with all components and wiring" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Figure 2 – exploded CAD with all components and wiring.</p>

---

## 1) The gap I saw (with humility)

I needed a throwaway-cheap platform to try ideas fast—vision thresholds, simple SLAM, tiny planners—without spending evenings soldering custom PCBs or weeks waiting for obscure parts. I found similar things, but nothing that quite matched my constraints: **under $30 per unit**, **order today/assemble this weekend**, **Wi-Fi camera on board**, ready to **scale to a dozen units** for swarm experiments.

Here are a few representative “near-misses” I found:

- **ESP32-CAM robot tutorials/kits** — great inspiration, proves feasibility; typically combine ESP32-CAM + L298N and a small chassis.  
  *Examples:* [Random Nerd Tutorials — ESP32‑CAM Robot Car][ref-rnt-car], [DroneBot Workshop — ESP32‑CAM Robot Car][ref-dronebot]
- **2WD “TT motor” car chassis kits** — readily available across vendors in many flavors; cheap and fast to ship.  
  *Examples:* [Cytron 2WD Smart Robot Chassis][ref-cytron-2wd], [Cytron Robot Chassis Catalog][ref-cytron-chassis]
- **TT motors in bulk** — packs of 10 are widely sold; pricing varies by vendor but is often economical for swarms.  
  *Examples:* [Amazon TT/L9110S Driver Listing][ref-amz-l9110s]

These gave me confidence the parts are commodity — but I still wanted a single, minimal **BOM** and a **clean, repeatable build** that’s easy to scale.

---

## 2) Design goals

- As cheap as possible without exotic parts.  
- Order-today parts from common marketplaces.  
- Zero-to-rolling in an afternoon (laser-cut or 3D-printed plate; no custom PCB).  
- Real on-board vision (**ESP32-CAM** streams video and does simple CV).  
  *Reference:* [Random Nerd Tutorials — ESP32‑CAM Video Streaming][ref-rnt-stream]  
- Swarm-ready (Wi-Fi control, per-unit IDs, bulk-buy motors).  
  *Reference:* [Amazon L9110S Motor Driver][ref-amz-l9110s]  
- Upgrade path to bigger compute/sensors (e.g., **Jetson Orin Nano + RPLIDAR**).

---

## 3) Final architecture

- **Drive:** Differential, 2× TT motors with wheels (cheap, ubiquitous).  
- **Motor driver:** L298N dual H-bridge module for two DC motors (5–35 V input; ~2 A/bridge).  
  *References:* [Components101 — L298N Module][ref-c101-l298n], [Smart Prototyping — L298N Board][ref-smart-l298n]  
- **Brain + camera:** ESP32-CAM (handles video streaming and PWM/GPIO for motor control). Typical draw ~**180–310 mA** depending on LED/stream; peaks can be higher—design your power margins accordingly.  
  *References:* [SunFounder — ESP32‑CAM (Power figures)][ref-sunfounder-esp32cam], [Core Electronics Forum — ESP32‑CAM Power Notes][ref-core-power]  
- **Power:** **4×AA** (alkaline or NiMH). Simple, safe charging workflows; swap packs in seconds. *(See battery notes below.)*  
  *References:* [Husarion — Batteries for Mobile Robots][ref-husarion-batt], [Grepow — NiMH vs LiPo Overview][ref-grepow-nimh]

**Why L298N?** It’s everywhere, cheap, and beginner-proof. Downsides: it’s an older bipolar design with noticeable voltage drop and heat versus modern MOSFET drivers. If you want leaner and more efficient, consider **L9110S, TB6612, MX1508, or DRV8833**—they’re widely used for small TT motors.  
*References:* [Elecrow — L9110S Module][ref-elecrow-l9110s], [RobotShop Community — L298N Voltage Drop Discussion][ref-robotshop-l298n]

---

## 4) Bill of Materials (BOM)

| Item | Example / Notes | Qty | Est. unit cost* |
|---|---|:---:|:---:|
| TT motor + wheel | 10-packs widely sold; buy 2 per robot *(see [Cytron 2WD kit][ref-cytron-2wd])* | 2 | $1–$2 ea (bulk) |
| L298N motor driver | Off-the-shelf module ([Smart Prototyping][ref-smart-l298n]) | 1 | $3–$6 |
| ESP32-CAM | AI-Thinker or equivalent ([Last Minute Engineers guide][ref-lme-esp32cam]) | 1 | $6–$12 |
| 4×AA holder + AAs | Alkaline or NiMH rechargeables ([Husarion guide][ref-husarion-batt]) | 1 | $3–$8 |
| Chassis | 3D-printed plate or generic 2WD kit base ([Cytron catalog][ref-cytron-chassis]) | 1 | $0 (print)–$10 |
| Odds & ends | M3 hardware, wires, header leads | — | $2–$4 |

<small>*Pricing varies by vendor/region; shown to convey order-of-magnitude based on common listings.*</small>

**Target cost (single unit):** $20–$30 if you already have basic tools and can print/laser-cut the plate; less per unit when building a small swarm (motors in **10-packs** help a lot).

---

## 5) Reference designs I learned from

- **ESP32‑CAM Car (web server + streaming + control).** [Random Nerd Tutorials][ref-rnt-car]  
- **ESP32‑CAM Robot Car (end-to-end guide and power notes).** [DroneBot Workshop][ref-dronebot]  
- **ESP32‑CAM 4WD (ESP32‑CAM + L298N example).** [Keyestudio Docs][ref-keyestudio-4wd]  
- **Generic 2WD TT chassis kits (fastest way to a rolling base).** [Cytron 2WD Smart Robot Chassis][ref-cytron-2wd]

These informed my choices and helped me avoid dead ends.

---

## 6) Key design decisions (and the trade-offs)

### 6.1 Motor driver: L298N vs “modern” modules
- **L298N:** bulletproof availability; dual H-bridge; up to ~2 A/bridge; handles 5–35 V. **Cons:** larger voltage drop → less torque at the wheel for a given battery, more waste heat. ([Components101 overview][ref-c101-l298n])  
- **L9110S / TB6612 / MX1508 / DRV8833:** more efficient for low-voltage TT motors; smaller, cooler, better battery life — but sometimes a touch less “forgiving” for absolute beginners. ([Elecrow module][ref-elecrow-l9110s])

**My call:** ship the baseline with **L298N** for ubiquity, and document drop-in alternatives.

### 6.2 Battery: AA (alkaline/NiMH) vs Li-ion/LiPo
- **AA (alkaline or NiMH):** dead-simple logistics, easy to source globally, and charging NiMH is comparatively forgiving. Great for classroom and hobby contexts where safety and simplicity matter. ([Husarion guide][ref-husarion-batt])  
- **Li-ion/LiPo:** higher energy density, better cycle life and charge retention, but require proper chargers and safety practices; excellent when you need more runtime/power. **LiFePO₄** is a safer lithium chemistry worth considering. ([Tritek Battery — LiFePO₄ advantages][ref-tritek-advantages], [EcoFlow — LiFePO₄ benefits][ref-ecoflow-benefits])

**My call:** default to **AA** for the “hassle-free” build; document a **Li-ion/LiFePO₄** option for advanced users.

### 6.3 Compute: ESP32-CAM for “one-chip vision + control”
**ESP32-CAM** integrates Wi-Fi + camera; it can stream MJPEG over a web page while also toggling GPIOs for motor control. Budget ~**180–310 mA @ 5 V** during streaming with flash variations; allow margin for peaks. **Power it from the 5 V pin** rather than 3.3 V for stability. ([SunFounder power figures][ref-sunfounder-esp32cam]; [Core Electronics forum][ref-core-power])

---

## 7) Electrical + firmware overview

**Wiring (baseline):**
- ESP32-CAM **5V** → battery rail (through switch); **GND** common with L298N.  
- ESP32-CAM two **GPIOs per motor** (e.g., GPIO12/13 → IN1/IN2; GPIO14/15 → IN3/IN4).  
- L298N **ENA/ENB** either tied **HIGH** or **PWM’d** from ESP32-CAM timer channels.  
- Motors to **L298N OUT1/OUT2** and **OUT3/OUT4**.  
- Optional **5 V buck** (if you go Li-ion): feed L298N + ESP32-CAM stable 5 V.

**Firmware sketch (high level):**
- Start **AP** or join **Wi‑Fi**; expose a small web UI with live video.  
- Control page: forward/back/left/right/stop + **speed sliders (PWM)**.  
- Add a **debug overlay** (FPS, battery, RSSI).  
- **CV mode:** HSV thresholding or **AprilTags** → send motor commands accordingly.

For a jump‑start, see [Random Nerd Tutorials — ESP32‑CAM Car][ref-rnt-car].

---

## 8) Build + test plan

**Mechanical (60–90 min):**
- Print/laser the plate; mount TT motors and caster/skid.  
- Standoff the ESP32-CAM and L298N on the top deck for airflow and Wi‑Fi.  
- Zip‑tie cable relief; label motor polarity for consistency across a swarm.

**Electrical (45–60 min):**
- Wire battery → switch → 5 V rail; common grounds.  
- ESP32-CAM GPIOs → L298N inputs; verify ENA/ENB PWM.  
- Bring‑up test on bench: stream video; jog motors at low duty.

**Software (30–60 min):**
- Flash web‑UI + streaming; verify control latency.  
- Add **fail‑safes** (stop on link loss; watchdog).  
- Optional: baseline CV (color blob or tag detection) to demo autonomy.

**Acceptance checks:**
- Video ≥ **10–15 FPS** at QVGA; latency **< 300 ms** on local Wi‑Fi.  
- Robot drives straight **±5° over 1 m** after calibration.  
- **Runtime goal:** **20–40 minutes** on AA NiMH depending on duty cycle (expect less with Alkaline at high loads).

---

## 9) Swarm‑readiness

- **Cost scaling:** motors in 10‑packs and bulk standoffs/wire reduce per‑unit cost.  
- **Identity & control:** per‑robot hostname/SSID; WebSocket or UDP broadcast control; start with simple “multi‑teleop” page.  
- **Synchronization:** assign time slots for motion; add AprilTags or colored beacons for global pose if desired.  
- **Fleet safety:** idle timeout, geo‑fences (grid cells on floor), and speed caps.

---

## 10) Upgrade path: when you need “real” sensors/compute

The same chassis pattern can be scaled up (wider plate, stiffer mounts) to carry **Jetson Orin Nano** and an **RPLIDAR** for mapping/VO. Swapping the **L298N** for a **TB6612** or larger driver, adding **encoders**, and bumping to **Li‑ion/LiFePO₄** will give you the headroom to run real‑time SLAM stacks.

*(For inspiration on higher‑end, off‑the‑shelf kits that bundle Orin Nano with a chassis/sensors, vendors exist — but they’re orders of magnitude more expensive, which is exactly the niche this project avoids.)* See examples at [Oz Robotics][ref-ozrobotics-orin].

---

## 11) CAD + repo

- **CAD:** parametric plate (**DXF + STEP**) with hole patterns for TT motors, L298N, ESP32‑CAM standoffs, and battery holder.  
- **Firmware:** minimal web‑UI; motor API; optional CV module.  
- **Docs:** wiring diagram, pin map, and a one‑page bring‑up checklist.

**Images**  
Figure 3 – wiring diagram (ESP32‑CAM ↔ L298N ↔ TT motors).  
Figure 4 – dimensioned top plate (DXF/STEP).

---

## 12) Results

With a single afternoon build, I can:
- **Teleop via browser + live video.**  
- **Run simple HSV thresholding** on‑board and do reactive behaviors.  
- **Duplicate 5–10 robots cheaply** for group behaviors.

Thermals are manageable; the **L298N** gets warm at sustained high duty due to voltage drop (expected for this class of driver). If endurance matters, switch to a modern driver to reclaim efficiency. ([RobotShop Community discussion][ref-robotshop-l298n])

---

## 13) Battery note (why I chose AA for the baseline)

I defaulted to **AA** for the beginner/hobbyist build: cheap, globally available, and straightforward charging/swap logistics—great for classrooms and casual use. **NiMH AAs** are especially forgiving to charge compared to **Li‑ion/LiPo**, which require stricter chargers/procedures. If you need more runtime or punch, step up to **Li‑ion/LiFePO₄** with a proper buck to 5 V and clear safety practices. ([EcoFlow — LiFePO₄ overview][ref-ecoflow-what-is], [Husarion — battery guide][ref-husarion-batt], [Grepow — NiMH vs LiPo][ref-grepow-nimh])

*(If you disagree and prefer Li‑ion from day one: totally valid for experienced builders. I’ll include a **LiFePO₄** variant in the repo with a recommended buck and charge‑safety checklist.)* ([Kamada Power — LiFePO₄ packs][ref-kamada])

---

## 14) What this design is (and isn’t)

**Pros**
- Ultra‑low cost, fast to source + assemble.  
- All‑in‑one **ESP32‑CAM** simplifies vision + control. ([Random Nerd Tutorials][ref-rnt-stream])  
- Great for swarms—buy parts in bulk and duplicate builds.  
- Teachable—students can build and understand the whole stack.

**Cons**
- Limited torque + control fidelity with **TT motors** (no encoders by default).  
- **L298N** efficiency isn’t stellar; a modern driver extends runtime. ([RobotShop Community][ref-robotshop-l298n])  
- Wi‑Fi latency and camera bandwidth cap your autonomy loop rates.

**When another platform may be better**
- If you need precise **odometry**, choose gearmotors with **encoders** or a kit that includes them.  
- If you need **long runtime/heavier payloads**, start with **Li‑ion/LiFePO₄** and a more efficient driver.  
- If you need **ROS2 + 2D lidar mapping**, jump straight to a larger base that can carry **Jetson + RPLIDAR** cleanly.

---

## 15) What’s next

- Publish the open **CAD**, **wiring**, and **starter firmware**.  
- Add **AprilTag** support and a **calibration page** (motor trim, camera exposure, HSV presets).  
- Provide a **modern‑driver** variant (**TB6612/L9110S**) BOM + wiring. ([Elecrow L9110S][ref-elecrow-l9110s])  
- Release a **swarm control page** (multi‑robot telemetry + teleop).

---

## References

[ref-rnt-car]: https://randomnerdtutorials.com/esp32-cam-car-robot-web-server/ "Random Nerd Tutorials — ESP32‑CAM Robot Car"
[ref-dronebot]: https://dronebotworkshop.com/esp32cam-robot-car/ "DroneBot Workshop — ESP32‑CAM Robot Car"
[ref-rnt-stream]: https://randomnerdtutorials.com/esp32-cam-video-streaming-web-server-camera-home-assistant/ "Random Nerd Tutorials — ESP32‑CAM Video Streaming Web Server"
[ref-c101-l298n]: https://components101.com/modules/l293n-motor-driver-module "Components101 — L298N Motor Driver Module"
[ref-smart-l298n]: https://www.smart-prototyping.com/L298N-Dual-H-bridge-Motor-Driver-Board "Smart Prototyping — L298N Board"
[ref-sunfounder-esp32cam]: https://docs.sunfounder.com/projects/galaxy-rvr/en/latest/hardware/cpn_esp_32_cam.html "SunFounder — ESP32‑CAM (power figures)"
[ref-core-power]: https://forum.core-electronics.com.au/t/esp32-cam-power-supply/16059 "Core Electronics Forum — ESP32‑CAM Power"
[ref-husarion-batt]: https://husarion.com/blog/batteries-for-mobile-robots/ "Husarion — Batteries for Mobile Robots"
[ref-grepow-nimh]: https://www.grepow.com/blog/lipo-battery-vs-nimh-battery-what-is-the-difference.html "Grepow — LiPo vs NiMH"
[ref-elecrow-l9110s]: https://www.elecrow.com/l9110-dualchannel-hbridge-motor-driver-module-12v-800ma-p-826.html "Elecrow — L9110S Motor Driver Module"
[ref-robotshop-l298n]: https://community.robotshop.com/forum/t/power-for-motor-drivers-l298n-and-l9110s-and-nanoatmega328-based-bot/11996 "RobotShop Community — L298N voltage drop discussion"
[ref-lme-esp32cam]: https://lastminuteengineers.com/getting-started-with-esp32-cam/ "Last Minute Engineers — ESP32‑CAM Guide"
[ref-cytron-2wd]: https://www.cytron.io/p-2wd-smart-robot-car-chassis "Cytron — 2WD Smart Robot Car Chassis"
[ref-cytron-chassis]: https://www.cytron.io/c-robot-chassis "Cytron — Robot Chassis Catalog"
[ref-keyestudio-4wd]: https://docs.keyestudio.com/projects/KS5024/en/latest/docs/4WD%20Camera%20Robot%20Car.html "Keyestudio — ESP32‑CAM 4WD Camera Robot Car"
[ref-tritek-advantages]: https://tritekbattery.com/7-advantages-of-lifepo4-batteries/ "Tritek Battery — Advantages of LiFePO₄"
[ref-ecoflow-benefits]: https://www.ecoflow.com/us/blog/benefits-of-lithium-iron-phosphate-batteries "EcoFlow — Benefits of LiFePO₄ batteries"
[ref-ozrobotics-orin]: https://ozrobotics.com/shop/jetauto-pro-ros-robot-car-with-vision-robot-arm-python-standard-kit-with-jetson-orin-nano-4gb/ "Oz Robotics — Jetson Orin Nano robot platform (example)"
[ref-ecoflow-what-is]: https://www.ecoflow.com/us/blog/what-is-lifepo4-battery "EcoFlow — What Is LiFePO₄?"
[ref-kamada]: https://www.kmdpower.com/12v-lifepo4-battery/ "Kamada Power — LiFePO₄ packs"
[ref-amz-l9110s]: https://www.amazon.com/L9110S-Channel-Drive-Module-Driver/dp/B07WBZSH5H "Amazon — L9110S Motor Driver (bulk examples)"