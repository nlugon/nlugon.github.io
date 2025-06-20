---
layout: page
title: Applied Machine Learning
description: Implementing fundamental machine learning algorithms
img: assets/img/gmm.png
importance: 11
category: School Projects
---



<div class="row justify-content-center">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/aml-intro.jpeg" title="PCA Example" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

Below is summarized six small projects completed as part of a machine learning course with EPFL. Each project focuses on implementing a fundamental algorithm or technique in machine learning, and for this course, the implementations were evaluated in MATLAB.

---

<h4>1. Principal Component Analysis (PCA)</h4>
<ul>
  <li><strong>Objective:</strong> Implement PCA for dimensionality reduction and apply it to real-world datasets.</li>
  <li><strong>Applications:</strong>
    <ul>
      <li>Dimensionality reduction on the Fisher-Iris dataset.</li>
      <li>Image compression.</li>
    </ul>
  </li>
  <li><strong>Key Features:</strong>
    <ul>
      <li>Covariance matrix calculation and eigenvalue decomposition.</li>
      <li>Projection to lower-dimensional spaces and data reconstruction.</li>
      <li>Explained variance analysis for optimal component selection.</li>
    </ul>
  </li>
</ul>

<div class="row justify-content-center">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/pca.png" title="PCA Example" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

---

<h4>2. K-Means Clustering</h4>
<ul>
  <li><strong>Objective:</strong> Implement and evaluate the K-Means clustering algorithm.</li>
  <li><strong>Applications:</strong>
    <ul>
      <li>Clustering a high-dimensional dataset.</li>
      <li>Developing a recommendation system.</li>
    </ul>
  </li>
  <li><strong>Key Features:</strong>
    <ul>
      <li>Centroid initialization and assignment.</li>
      <li>Metrics such as RSS, AIC, and BIC for model evaluation.</li>
      <li>Visualization of cluster boundaries and optimization of the number of clusters.</li>
    </ul>
  </li>
</ul>

<div class="row justify-content-center">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/kmeans.png" title="K-Means Example" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

---

<h4>3. K-Nearest Neighbors (KNN)</h4>

<ul>
  <li><strong>Objective:</strong> Implement KNN for classification.</li>
  <li><strong>Applications:</strong>
    <ul>
      <li>Testing on synthetic and real-world datasets.</li>
    </ul>
  </li>
  <li><strong>Key Features:</strong>
    <ul>
      <li>Distance computation using L1, L2, and Linf norms.</li>
      <li>Majority voting mechanism for classification.</li>
      <li>Evaluation of classifier performance with accuracy metrics.</li>
    </ul>
  </li>
</ul>

<div class="row justify-content-center">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/knn.png" title="KNN Example" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

---

<h4>4. Gaussian Mixture Models (GMM)</h4>
<ul>
  <li><strong>Objective:</strong> Develop the GMM algorithm using Expectation-Maximization (EM) for parameter estimation.</li>
  <li><strong>Applications:</strong>
    <ul>
      <li>Clustering datasets with multi-modal distributions.</li>
    </ul>
  </li>
  <li><strong>Key Features:</strong>
    <ul>
      <li>Implementation of full, diagonal, and isotropic covariance matrices.</li>
      <li>Model evaluation using AIC and BIC metrics.</li>
      <li>Visualization of Gaussian components.</li>
    </ul>
  </li>
</ul> 

<div class="row justify-content-center">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/gmm.png" title="GMM Example" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

---

<h4>5. GMM Applications</h4>
<ul>
  <li><strong>Objective:</strong> Apply GMMs to classification, resampling, and regression tasks.</li>
  <li><strong>Applications:</strong>
    <ul>
      <li>Multi-class classification using GMMs.</li>
      <li>Generating synthetic data points via resampling.</li>
      <li>Gaussian Mixture Regression (GMR) for non-linear regression problems.</li>
    </ul>
  </li>
  <li><strong>Key Features:</strong>
    <ul>
      <li>Supervised classification with GMMs.</li>
      <li>Data augmentation through GMM-based resampling.</li>
      <li>Regression using the conditional density of GMMs.</li>
    </ul>
  </li>
</ul> 

<div class="row justify-content-center">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/gmm-apps.png" title="GMM Applications Example" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

---

<h4>6. Neural Networks (NN)</h4>
<ul>
  <li><strong>Objective:</strong> Build a foundational neural network for binary classification.</li>
  <li><strong>Applications:</strong>
    <ul>
      <li>Training on synthetic datasets with visualization of decision boundaries.</li>
    </ul>
  </li>
  <li><strong>Key Features:</strong>
    <ul>
      <li>Implementation of activation functions (Sigmoid, ReLU, Leaky ReLU).</li>
      <li>Weight initialization strategies for stable convergence.</li>
      <li>Mini-batch learning and visualization of training progress.</li>
    </ul>
  </li>
</ul>

<div class="row justify-content-center">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/nn.png" title="Neural Networks Example" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

---

<h4>How to Use</h4>
<ul>
  <li>GitHub Repository <a href="https://github.com/nlugon/machine-learning">here</a>.</li>
  <li>Each project folder contains:
    <ul>
      <li>MATLAB scripts for implementation (.m files).</li>
      <li>Dataset files for testing and evaluation.</li>
      <li>Instructions for running the code and visualizing results.</li>
    </ul>
  </li>
</ul>
