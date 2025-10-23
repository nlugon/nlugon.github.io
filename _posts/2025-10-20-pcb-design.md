---
layout: post
title: "My weak spot: the electronics side"
date: 2025-10-20 00:04:10
description: "I love CAD and code, but my wiring is chaos. This is me getting serious about a clean PCB for my robotics projects."
tags: electronics pcb kicad prototyping robotics
categories: robotics
thumbnail: assets/img/pcb-first-try.jpg
published: true
---

# My weak spot: the electronics side

So I really love using **CAD** to design and 3D print stuff.  
And I love **programming** stuff.

And when it comes to getting **sensors, processors, actuators, power sources** and all connected together, I just default to the **breadboard** and a whole bunch of very loose wires of all colors all over the place. And I just buy off-the-shelf boards or shields for power management. It's a pretty bad use of space, not very robust, and does not look too great.

So one day I decided to actually do something about this and **design**, **have manufactured and delivered** (because I currently don't have the means to make my own PCBs), and **test** a clean PCB for my robotics projects. So here were the steps in this endeavor:

---

## step 0: know what I actually want to design

- Take inventory of **what the robot actually needs** (now, not someday): MCU/SoC, sensors, motor drivers, radio, power in/out, connectors.  
- List **voltages & currents** (5 V, 3.3 V, motor rail; peak vs average).  
- Pin map: what GPIOs, PWMs, I²C/SPI/UART do I actually need? Any interrupts?  
- Sketch **block diagram** and **room for screw holes** and mounting.  
- Decide on **connectors** (JST-XH/PH, headers, USB-C, XT30, etc.).  

*(This alone already reduces the “spaghetti wires” problem a lot.)*

---

## step 1: figure out what software to use

- Popular tools a hobbyist like me would consider: **KiCad**, **EasyEDA**, **Autodesk Fusion/EAGLE**, **Altium Designer** (overkill for me), **CircuitMaker**.
- I chose **KiCad**, because it made the most sense for me:
  - **Open‑source** and free, no lock‑in.  
  - Solid **schematic ↔ PCB** flow, push‑and‑shove router, good **DRC**.  
  - Great **3D viewer** and STEP export (helps with mechanical fit).  
  - Plays nicely with common fabs (Gerbers/Drill, pick‑and‑place, BOM).  
  - Big community and tutorials.  
  - Git‑friendly for versioning.

Refs: [KiCad official][ref-kicad], [KiCad features][ref-kicad-features]

---

## step 2: learn how to use the software

What helped me get from “zero” to “I can make a small board” fast:

- **Getting Started** (KiCad official guide). [link][ref-kicad-start]  
- **Digi‑Key KiCad Series** (schematic → layout → fab). [link][ref-dk-kicad]  
- **Getting to Blinky** (Contextual Electronics) — classic first project. [link][ref-gtb]  

Optional rabbit holes later: panelization, custom footprints, length‑matching, stitching vias.

---

## step 3: build first prototypes (before sending anything to fab)

- Draw the **schematic** (labels > long nets). Create/verify footprints.  
- **ERC/DRC** early and often.  
- **Power first**: place regulators, bulk caps, decoupling. Keep loops short.  
- **Test points** for key rails and signals.  
- Quick **paper print** of the board at 1:1 to sanity‑check connectors and mounting.  
- If possible, do a **perfboard “proto”** for the riskiest sub‑circuit (e.g., a buck or H‑bridge) to confirm thermals/noise.  
- Generate **BOM + pick‑and‑place** to make sure references and rotations look sane.

Good manufacturing primers: [PCB fabrication basics][ref-pcbway-guide], [JLC DFM notes][ref-jlc-dfm].

---

## step 4: receive and test the board

- **Continuity** + quick **visual** inspection (silkscreen, orientation, polarity marks).  
- First power‑on with a **current‑limited bench supply** (start low; sneak up).  
- Measure rails **unloaded**, then with a **dummy load**.  
- Plug in MCU **last**. Smoke test with **firmware that does nothing fancy**.  
- Check **signals on a scope** (clocks, PWM, noisy DC‑DC).  
- Keep a **bring‑up checklist** so you don’t skip steps at 1 a.m.

Bench habits: current limiting, ESD strap/mat, and having a **known‑good** USB cable (seriously).

---

## step 5: integrate and show the robot works

- Mount board in the robot, route cables, strain‑relief.  
- Run your **functional tests** (motors, sensors, comms).  
- Capture **photos / notes** for the next revision, including what broke and what worked.  
- And write the blog post about all of this (well actually, I started writing this since the beginning).

---

## What I’ll improve next

- Cleaner **power tree** (buck → LDO, separate analog/digital grounds where needed).  
- **Connectors** that match how I actually service the robot in the field.  
- More **test points** and some **LEDs** for life signs.  
- A basic **template repo** so the next board starts faster.

---

## References

[ref-kicad]: https://www.kicad.org/ "KiCad"
[ref-kicad-features]: https://www.kicad.org/about/kicad/ "KiCad — Features"
[ref-kicad-start]: https://docs.kicad.org/ "KiCad — Getting Started"
[ref-dk-kicad]: https://www.digikey.com/en/resources/videos/kicad "Digi‑Key — KiCad Series"
[ref-gtb]: https://contextualelectronics.com/courses/getting-to-blinky-5-0/ "Contextual Electronics — Getting to Blinky"
[ref-pcbway-guide]: https://www.pcbway.com/blog/News/ "PCBWay — Fabrication Basics"
[ref-jlc-dfm]: https://jlcpcb.com/help/article/23-How-to-improve-the-Design-for-Manufacturing-DFM "JLCPCB — DFM Notes"