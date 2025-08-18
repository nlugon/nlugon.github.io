---
layout: page
title: 3D Vehicle Localization
description: Applied Deep Learning Course
img: assets/video/dlav.mp4
importance: 7
category: School Projects
---



<div class="row justify-content-center">
    <div class="col-sm-12 mt-3 mt-md-0">
        {% include video.liquid path="assets/video/dlav.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
    </div>
</div>


The goal of this project was to **find a state-of-the-art paper on monocular 3D vehicle localization and improve upon it**. We began with **MonoCon (AAAI 2022)**, a model that demonstrated high accuracy and real-time performance. While effective, MonoCon only processes single frames, meaning vehicles disappear from detection once obstructed or out of view.  

## Improvements with Multi-Object Tracking (MOT)  

To address this limitation, we extended the model with **MOT techniques**:  
- **3D IoU + Hungarian Algorithm** – used to associate detections across frames into tracklets.  
- **Kalman Filter** – introduced for motion prediction of each tracked object.  
- **LSTM Networks** – later replaced the Kalman Filter, enabling smoother updates of velocity and position through sequence learning.  

## Results  

With these improvements, the model can **continue localizing vehicles even when partially hidden or cut off by the camera frame**. We validated our approach on the **KITTI dataset** and on **our own driving footage in Lausanne, Switzerland**, showing **large gains in robustness** compared to the original model.  



Github <a href="https://github.com/nlugon/3dvehicule_localization_gr24">here</a>.

YouTube <a href="https://www.youtube.com/watch?v=XafVQ4Ar4-E">here</a>.



