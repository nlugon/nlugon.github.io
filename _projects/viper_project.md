---
layout: page
title: Lunar Rover Localization
description: Master thesis, conducted with NASA Ames Research Center
img: assets/img/sim.png
importance: 1
category: Highlights
related_publications: False
---


Leveraging Gaussian Processes to predict and correct wheel slip, using periodic pose updates from onboard visual odometry (VO) as approximations of ground truth. The developed pipeline prioritizes most recent and highest-confidence VO pose updates, and predicts uncertainty on slip predictions in addition to the predictions themselves. Results from simulated drives over slopes of 5º to 15º demonstrate reductions in wheel odometry error of 56-78%, and reductions in final pose error of 41%-58% through better initialization of VO.

Master's thesis report <a href="https://drive.google.com/file/d/1vC4_eQfzTcFWaHl5spJhuiHQGCyaOzNN/view">here</a>.

Master's thesis defense slides <a href="https://docs.google.com/presentation/d/1NKPrJSwf3uChUJK2-_mpL7byJoqE0WAIZHgFNBMzDSo/edit#slide=id.p">here</a>.


<div class="row justify-content-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/sim.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


