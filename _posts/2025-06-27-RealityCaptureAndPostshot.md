---
layout: post
title: Photogrammetry with Google Earth Studio
date: 2025-06-27 10:00:00
description: Simulating drone imagery with Google Earth Studio and generating a 3D model with RealityCapture
tags: photogrammetry gaussian-splatting nasa blender
categories: graphics
thumbnail: assets/img/AmesWindTunnelSpiralShort.gif
published: true
---

Recently explored the potential of **Google Earth Studio** as a tool for **planning and previewing drone photogrammetry workflows**. Around the same time, I was interested in creating small 3D-printed models of notable infrastructure at NASA Ames, such as the **NFAC (the world’s largest wind tunnel)**. Since Google Earth already provides detailed 3D terrain and structure data, I thought it would be interesting to test whether Google Earth Studio could serve as a simulation tool for photogrammetry.

Getting started was fairly straightforward. After generating a short animation, I used **RealityCapture** to convert the footage into a textured mesh, which I then cleaned up in **Blender** and prepared for 3D printing. As a secondary experiment, I also used **Jawset PostShot** to generate a Gaussian Splat from the same footage. While the splat wasn't perfect due to limited sky coverage in the animation, the overall results were compelling. Here's a quick overview of the workflow and results.

---

#### Step 1: Google Earth Studio – Generate Animation

[Google Earth Studio](https://www.google.com/earth/studio/) is a browser-based animation tool from Google that allows you to create cinematic flyovers of real-world locations. Documentation is available [here](https://earth.google.com/studio/docs/). To access it:

- Go to the [Google Earth Studio website](https://www.google.com/earth/studio/)
- Click **Try Earth Studio**
- Sign in with a Google account and briefly describe your intended use

I used the spiral orbit template centered on the NFAC. It’s possible to import `.kml` files into Earth Studio—though based on my testing, these files only support overlays and paths for visualization within the animation, rather than actual flight control.

{% include figure.liquid path="assets/img/GoogleEarthStudio1.png" title="Google Earth Studio templates" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Google Earth Studio templates</p>

{% include figure.liquid path="assets/img/GoogleEarthStudio2.png" title="Google Earth Studio interface" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Google Earth Studio main interface</p>

I exported the rendered animation as an `.mp4` file, which could be directly imported into both **RealityCapture** and **PostShot**.

{% include figure.liquid path="assets/img/AmesWindTunnelSpiralShort.gif" title="Google Earth Studio animation over NASA Ames wind tunnel. Attribution: Google Earth, Vexcel Imaging US, Inc." class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Animation flyover of the NFAC at NASA Ames. Attribution: Google Earth, Vexcel Imaging US, Inc.</p>

---

#### Step 2: RealityCapture – Convert Video to Mesh

In **RealityCapture**, I imported the `.mp4` and allowed it to extract approximately 200 frames. Using mostly default settings, I ran the photogrammetry pipeline to reconstruct a textured mesh.

{% include figure.liquid path="assets/img/AmesWindTunnel-RealityCapture.png" title="Photogrammetry result in RealityCapture" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Generated textured mesh from Google Earth Studio footage</p>

After reconstruction and simplification, I exported the mesh as a `.obj` file for further editing in Blender.

---

#### Step 3: Blender – Mesh Editing

Photogrammetry-generated meshes often require cleanup. I used **Blender** to refine the geometry and make adjustments to prepare the model for 3D printing.

{% include figure.liquid path="assets/img/AmesWindTunnel-Blender2.png" title="Editing the photogrammetry mesh in Blender" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Post-processing and cleanup of the mesh in Blender</p>

---

#### Step 4: Orca Slicer – 3D Printing

The refined mesh was then imported into **Orca Slicer**, where I set the model size and printing settings.

{% include figure.liquid path="assets/img/AmesWindTunnel-Orca.png" title="Preparing the mesh in Orca Slicer" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Slicing the model in Orca Slicer</p>

---

#### Step 5 – Print Results

The final 3D print is shown below. The full model is roughly 12 cm across, with the wind tunnel itself clearly recognizable. Some areas could benefit from additional mesh refinement to ensure consistent flatness. Surrounding facilities could be better defined but are in most part still identifiable. One idea for future iterations would be multi-material printing to better distinguish between buildings, roads, and grass areas.

{% include figure.liquid path="assets/img/AmesWindTunnel-3DPrint.jpg" title="3D Printed Model" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Final result: 3D printed model of the NFAC</p>

---

#### Extra: Jawset PostShot – Gaussian Splatting

For comparison, I also processed the video using **Jawset PostShot** to create a **Gaussian Splat**. I used the *splat3 radiance field* profile and left most settings at default. The tool extracted around 150 images and reconstructed a lightweight splat-based rendering.

One benefit of splatting is that it can represent background elements like the sky—something that traditional photogrammetry struggles with due to a lack of trackable features. However, because the animation included limited sky coverage, some regions remained unrepresented.

{% include figure.liquid path="assets/img/AmesWindTunnel-Postshot.png" title="Gaussian Splat rendering in PostShot" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Gaussian Splat generated from the same animation using PostShot</p>

---

#### Final Thoughts

**Google Earth Studio** is a surprisingly capable tool for generating flyover-style animations, and it integrates well with both **RealityCapture** and **Blender** for experimentation and prototyping. I was able to produce a reasonably accurate physical model of the NFAC, entirely from virtual footage.

As a future improvement, it would be useful if Earth Studio supported importing `.kml` flight plans for true photogrammetry simulation. Although `.kml` overlays are supported, they currently don’t drive the camera directly.

Interestingly, the watermark embedded in the rendered video didn’t show up in either the mesh or splat results—likely rejected as noise by both systems.

For those without access to drones or physical sites, I recommend exploring freely available photogrammetry datasets such as those hosted by [ESRI](https://www.esri.com/en-us/arcgis/products/arcgis-reality/resources/sample-drone-datasets). They’re a great resource for tuning your reconstruction pipeline before working with your own data.

---

**Tools Used:**

- [Google Earth Studio](https://www.google.com/earth/studio/)  
- [RealityCapture (RealityScan)](https://www.capturingreality.com/)  
- [Jawset PostShot](https://www.jawset.com)  
- [Blender](https://www.blender.org/)
- [Orca Slicer](https://orca-slicer.com)














