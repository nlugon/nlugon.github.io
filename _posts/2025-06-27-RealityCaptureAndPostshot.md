---
layout: post
title: Photogrammetry from Google Earth Studio
date: 2025-06-27 10:00:00
description: A quick experiment using Google Earth Studio, RealityCapture, and Jawset PostShot
tags: photogrammetry gaussian-splatting nasa blender
categories: graphics
thumbnail: assets/img/AmesWindTunnelSpiralShort.gif
published: true
---

I recently tested out **Google Earth Studio** and wondered how good of a tool it could be for **drone photogrammetry planning and previewing**. Prior to testing it out, I was also particularly interested in 3D printing small models of some of the cool infrastructure at NASA Ames, such as the **NFAC (largest wind tunnel in the world)**, and was looking into ways to obtain accurate 3D mesh data such as the data rendered in Google Earth. Figured it could be interesting to therefore use Google Earth Studio as a simulated 


Getting set up and testing Google Earth Studio was fairly straightforward, and after generating a short animation I used **Reality Capture** to convert the animation into a texturized mesh, which could then be edited in Blender and then 3D printed. For fun I also input the animation into **Jawset Postshot** to get a gaussian splat, although given the lack of sky in the animation it didn't turn out great. Here's a quick overview of the steps and results.



#### Step 1: Google Earth Studio

[Google Earth Studio](https://www.google.com/earth/studio/) is a browser-based animation tool from Google that lets you create cinematic flyovers of real-world landmarks. It's pretty well documented with documentation [here](https://earth.google.com/studio/docs/). To access it:

- Go to the [Google Earth Studio website](https://www.google.com/earth/studio/)
- Click **Try Earth Studio** 
- You'll most likely need a google email to continue, and fill in a form indicating what you wish to use Earth Studio for.

I started by using the spiral orbit template, centered around the NFAC. I noticed that it's possible to import .kml files, which could mean being able to directly import flight paths which, but seems like importing the .kml files only support displaying paths or overlays in the render.


{% include figure.liquid path="assets/img/GoogleEarthStudio1.png" title="GoogleEarthStudio1" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Google Earth Studio templates</p>


{% include figure.liquid path="assets/img/GoogleEarthStudio2.png" title="GoogleEarthStudio1" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Google Earth Studio main interface</p>




I let Google Earth Studio render and export the animation as a **.mp4 video**, which could be imported both in **RealityCapture** and **PostShot** directly.

{% include figure.liquid path="assets/img/AmesWindTunnelSpiralShort.gif" title="Google Earth Studio animation over NASA Ames wind tunnel. Attribution: Google Earth, Vexcel Imaging US, Inc." class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Google Earth Studio animation over the NFAC at NASA Ames. Attribution: Google Earth, Vexcel Imaging US, Inc.</p>



#### Step 2: RealityCapture

In RealityCapture, I loaded the video, let it extract the frames (about 200 images), and ran the photogrammetry pipeline with pretty much default settings.

{% include figure.liquid path="assets/img/AmesWindTunnel-RealityCapture.png" title="Photogrammetry result in RealityCapture" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Generated texturized mesh in Reality Capture</p>


After texturing and simplifying, I exported the mesh in `.obj` format for post-editing in Blender.

---

#### Step 3: Blender

RealityCapture meshes can look rough without cleanup. So I brought the model into **Blender** to refine some of the geometry and make it shape it into a nice 3D printable model.

{% include figure.liquid path="assets/img/AmesWindTunnel-Blender2.png" title="Editing the photogrammetry mesh in Blender" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Post-editing with Blender</p>

---

#### Step 4: Orca Slicer

Last step: slicing the refined model from Blender such that it can be 3D printed.

{% include figure.liquid path="assets/img/AmesWindTunnel-Orca.png" title="Editing the photogrammetry mesh in Blender" class="img-fluid rounded z-depth-1" zoomable=true %}
<p class="text-muted text-center mt-2">Preparing for 3D printing with Orca Slicer</p>

---

#### Extra: Jawset PostShot

For extra fun I also input the animation into **PostShot**, which I used to generate a **Gaussian Splat** rendering from its extracted frames. I selected the splat3 radiance field profile to get a relatively quick preview and kept the default settings, with 150 images extracted. Looking at the results, one cool thing with Gaussian Splats is that the sky can be represented, which is something photogrammetry cannot do due to the sky's lack in trackable features. Given that the animation generated with Google Earth Studio included few camera angles that captured the sky, many sky regions remain unrepresented 

{% include figure.liquid path="assets/img/AmesWindTunnel-Postshot.png" title="Gaussian Splat rendering in PostShot" class="img-fluid rounded z-depth-1" zoomable=true %}


---

#### Final Thoughts


Google Earth Studio is a very cool and quite capable tool for generating flyover-type animations. Even without drone footage, tools like **Google Earth Studio** + **RealityCapture** or **PostShot** can let you somewhat practice building 3D reconstructions from scratch. I found quite interesting that the watermark in the video generated from Google Studio did not really show up in the mesh or gaussian splat results, suggesting both tools rejected it pretty welll as some form of noise. I did also find out that there are readily available image datasets for drone photogrammetry (such as those hosted by [ESRI](https://www.esri.com/en-us/arcgis/products/arcgis-reality/resources/sample-drone-datasets)), and if you don't currently have a drone these resources would be a lot more relevant for practicing tweaking best photogrammetry settings. 



---

**Tools Used:**

- [Google Earth Studio](https://www.google.com/earth/studio/)  
- Reality Capture (Now [RealityScan](https://www.capturingreality.com/))
- [Jawset PostShot](https://www.jawset.com)  
- [Blender](https://www.blender.org/)  








