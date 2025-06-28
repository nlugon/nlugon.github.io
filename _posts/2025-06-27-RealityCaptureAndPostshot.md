---
layout: post
title: Google Earth Studio, Photogrammetry and Gaussian Splatting 
date: 2025-06-27 10:00:00
description: A quick experiment using Google Earth Studio, RealityCapture, and Jawset PostShot
tags: photogrammetry gaussian-splatting nasa blender
categories: graphics
thumbnail: assets/img/AmesWindTunnelSpiralShort.gif
published: true
---

Messing around with cinematic flyovers and 3D reconstruction tools to see what kind of mesh or splat model I could get. The goal: use **Google Earth Studio** to generate a video of a place I find cool (in this case, the **biggest wind tunnel in the world at NASA Ames**) and push it through **RealityCapture** and **Jawset PostShot**.

---

#### Step 1: Google Earth Studio – Access and Animation

[Google Earth Studio](https://earth.google.com/studio/) is a browser-based animation tool from Google that lets you create cinematic flyovers of real-world landmarks. To access it:

- Go to the [Google Earth Studio website](https://earth.google.com/studio/)
- Click **Request Access** 
- Wait a little for approval

At the time of testing it felt fairly intuitive to use, although felt some aspects of the interface could be improved. I created a **spiral orbit animation** centered around the wind tunnel at NASA Ames Research Center.

{% include figure.liquid path="assets/img/AmesWindTunnelSpiralShort.gif" title="Google Earth Studio animation over NASA Ames wind tunnel" class="img-fluid rounded z-depth-1" zoomable=true %}

I let Google Earth Studio render and export the animation as a **.mp4 video**, which could be imported both in **RealityCapture** and **PostShot** directly.

---

#### Step 2: From Video to Frames (Automatically)

Here’s the convenient part: both **RealityCapture** and **Jawset PostShot** accept `.mp4` video files directly and internally extract individual frames for processing.

This removes the need for tools like **FFmpeg**, though it’s good to know they exist. (For a different project, I’ve also used **VLC** to generate snapshots manually or with at a fixed frequency.)

---

#### Step 3: RealityCapture – From Images to Mesh

In RealityCapture, I loaded the video, let it extract the frames, and ran the photogrammetry pipeline. It worked surprisingly well—even with Google Earth’s hybrid image data.

{% include figure.liquid path="assets/img/AmesWindTunnel-RealityCapture.png" title="Photogrammetry result in RealityCapture" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">The generated mesh of the wind tunnel structure at NASA Ames</p>


After texturing and simplifying, I exported the mesh in `.obj` format for further use.

---

#### Step 4: Blender – Mesh Post-Processing

RealityCapture meshes can look rough without cleanup. So I brought the model into **Blender** to adjust lighting, refine the geometry, and test out various render styles.

{% include figure.liquid path="assets/img/AmesWindTunnel-Blender.png" title="Editing the photogrammetry mesh in Blender" class="img-fluid rounded z-depth-1" zoomable=true %}

This step isn’t necessary—but it’s fun, and gives you full control if you want to use the mesh in other scenes or visualizations.

---

#### Step 5: Jawset PostShot – Trying Out Gaussian Splatting

Next up was **PostShot**, which also handled the `.mp4` video directly and generated a **Gaussian Splat** rendering from its extracted frames.

While it doesn’t create a traditional mesh, the splat model is light, fast-loading, and visually smooth—perfect for web-based viewing or cinematic use.

{% include figure.liquid path="assets/img/AmesWindTunnel-Postshot3.png" title="Gaussian Splat rendering in PostShot" class="img-fluid rounded z-depth-1" zoomable=true %}

---

#### Final Thoughts

Here comes a part where I need to defend why I tried generating meshes out of an animation of a structure that iteslf is a mesh. Even if not being real drone footage, **Google Earth Studio** seems to be a good tool for experimentation and for potentially previewing paths in preperation for taking a drone out to do photogrammetry. In this case it was also a fun way to generate a 3D model of the big wind tunnel, without flying an actual drone on site, which would require maybe a few approvals given its location. Turns out I also discovered real-world data for photogrammetry to play with hosted by [ESRI](https://www.esri.com/en-us/arcgis/products/arcgis-reality/resources/sample-drone-datasets), so might get on to that 

Even without drone footage, tools like **Google Earth Studio** + **RealityCapture** or **PostShot** can let you somewhat practice building 3D reconstructions from scratch. I found quite interesting that the watermark in the video generated from Google Studio did not really show up in the mesh or gaussian splat results, suggesting both tools rejected it as a form of noise. I did also look up datasets for drone photogrammetry and they exist, consisting in images taken of real-world locations so could be worth experimenting with that.

---

**Tools Used:**

- [Google Earth Studio](https://earth.google.com/studio/)  
- [RealityCapture](https://www.capturingreality.com/)  
- [Jawset PostShot](https://www.jawset.com/postshot)  
- [Blender](https://www.blender.org/)  








