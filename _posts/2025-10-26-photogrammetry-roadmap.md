---
layout: post
title: Photogrammetry Learning Roadmap
date: 2025-10-26 8:00:00
description: Resources for fundamentals on Computer Vision, Vision-Based Localization and 3D Reconstruction
tags: camera photogrammetry computer-vision slam visual-odometry
categories: robotics
thumbnail: assets/img/skydio-feature-tracking.png
published: true
---

Cameras are key in a very wide variety of fields including robotics, augmented reality, and 3D mapping. Fundamentally, cameras measure light intensities over a large array of pixels, which is an efficient way to capture rich information about the world. With the right algorithms, this turns into geometry, motion, and semantics—fueling perception for robots and building machine-interpretable models of reality.

Below is a practical roadmap I’m writing for myself (and anyone else) to learn and quickly refresh the core ideas.



> Compact notes distilled and rephrased from **Cyrill Stachniss — Photogrammetry I**. Optimized for quick recall while keeping structure for deeper study later.

## On this page
- [Chapter 1 — Introduction to Photogrammetry](#chapter-1--introduction-to-photogrammetry)
- [Chapter 2 — Camera Basics & Propagation of Light](#chapter-2--camera-basics--propagation-of-light)
- [Chapter 3 — Image Histograms & Simple Point Operators](#chapter-3--image-histograms--simple-point-operators)
- [Chapter 4 — Histogram Equalization, Noise Variance Equalization & Binary Images](#chapter-4--histogram-equalization-noise-variance-equalization--binary-images)
- [Chapter 5 — Local Operators, Geometric Transforms & Cross Correlation](#chapter-5--local-operators-geometric-transforms--cross-correlation)
- [Chapter 6 — Visual Features: Keypoints & Descriptors](#chapter-6--visual-features-keypoints--descriptors)
- [Chapter 7 — Image Segmentation with Mean Shift](#chapter-7--image-segmentation-with-mean-shift)
- [Chapter 8 — Introduction to Classification](#chapter-8--introduction-to-classification)
- [Chapter 9 — Neural Networks](#chapter-9--neural-networks)
- [Chapter 10 — Math Basics & Homogeneous Transforms](#chapter-10--math-basics-&-homogeneous-transforms)
- [Chapter 11 — Camera Parameters, Calibration, and P3P](#chapter-11--camera-parameters-calibration-and-P3P)

- [References](#references)

---

### Image template (drop-in)
Use this snippet to insert figures with caption & label (works with many Jekyll themes):

---

# Chapter 1 — Introduction to Photogrammetry

**Definition.** Estimate **geometry** and **semantics** of objects from **images** (or similar sensors).  
**Core tasks.** Camera **poses**, **3D reconstruction**, **data association**, **detection/segmentation**, **semantic interpretation**.

**Why cameras?** Contact-free, dense/cheap/fast capture; human-interpretable; supports dynamics & real-time.  
**Limits.** Needs light; measures directional intensity only; occlusions; 3D→2D projection loses depth; other sensors may be more precise.

**Key idea.** An **image point defines a 3D ray**. Multiple views → **triangulation**.  
**Pipelines.** *Forward:* object → physics → intrinsics/extrinsics → image. *Inverse:* images + models + priors → object geometry/semantics.  
**Algorithms are central.** Sensors ≈ eyes; estimation ≈ brain → implement methods.

**Sensors & apps.** Industrial/consumer/aerial cameras, LiDAR; mapping, orthophotos, city models, cultural heritage, robotics, autonomous driving.

---

# Chapter 2 — Camera Basics & Propagation of Light

**What is measured?** Pixels integrate photons over exposure time → **intensity** per **direction** (ray).  
**Camera elements.** Lens & **aperture**, **shutter** (rolling/global), **sensor** (photosites), **A/D**, post-processing (incl. **demosaicing**).

**Color imaging.** Three-chip prism vs **single-chip CFA** (Bayer: 50% G); **demosaicing** adds artifacts (edges/moire).  
**Shutter.** **Rolling** (row-wise timing skew) vs **global** (simultaneous). Prefer global for geometry/high-speed scenes.

**Image formation.** **Pinhole** (one ray per point → sharp but dark). **Thin lens** (\(\tfrac{1}{f} = \tfrac{1}{z}+\tfrac{1}{z'}\)); aperture limits off-axis error.  
**Aperture & DoF.** Higher f-number → more DoF, less light; diffraction at extremes.

**Lenses & FOV.** Tele (narrow, minimal perspective), normal, wide (perspective exaggeration), **fisheye** (curved lines).  
**Aberrations.** **Distortion** (barrel/pincushion/mustache), **spherical**, **chromatic**, **astigmatism**, **coma**, **vignetting**. Mitigate via optics, aperture, calibration.

**Light models.** **Ray** (Snell/Fresnel, reversibility), **wave** (Maxwell; interference/diffraction; 400–700 nm), **particle** (photons).  
**Exposure triangle.** Shutter ↔ motion blur; Aperture ↔ DoF; ISO ↔ noise/gain.

*Suggested figures:* pinhole geometry; rolling vs global; exposure triangle.

---

# Chapter 3 — Image Histograms & Simple Point Operators

**Image & histogram.** Grayscale image \(I\) is an \(M\times N\) matrix; intensities in \([0,255]\).  
**Histogram** \(h[g]\): count of pixels with intensity \(g\). **CDF** \(H[g]\): cumulative counts/probabilities.

**What histograms tell.** Brightness (mean), contrast (variance), robustness (median). Shape hints at lighting/contrast, saturation/clipping.  
**Compute.** One pass over pixels → O(pixels). Color images: per-channel histograms.

**Operators (by support).** Global, **point**, local.  
**Point operators.** Map \(g\mapsto f(g)\) independently per pixel (fast via LUT of 256 entries).

**Linear transform.** \(g' = a\,g + b\).  
- Adjust brightness (\(b\)) and contrast (\(a\)).  
- Propagation of mean/std: \(\mu' = a\mu + b\), \(\sigma' = |a|\sigma\).  
- Contrast inversion: \(g' = 255 - g\).

**Nonlinear examples.** **Thresholding** → binary image; **quantization** → map to \(K\) discrete levels (K=2 gives binary).

*Suggested figures:* sample image & histogram; thresholding example; tone curve.

---

# Chapter 4 — Histogram Equalization, Noise Variance Equalization & Binary Images

## 4.1 Histogram Equalization (HE)

**Goal.** Redistribute intensities to **use full dynamic range** and enhance contrast.  
**Monotone mapping and PDFs.** For monotone \(b=f(a)\): area conservation yields  
\[ h_b(b) = h_a(a)\,\left|\frac{da}{db}\right|, \quad a=f^{-1}(b). \]
**HE solution.** Use **CDF** of input: \( b = \alpha\,H(a)+\beta \) → approximately uniform output (discrete case not perfectly flat).  
**Effect.** Boosts contrast, especially in low-contrast regions; may amplify noise. Variants: **AHE**, **CLAHE** (limits amplification).

*Suggested figures:* before/after image & histograms; CDF mapping sketch.

## 4.2 Noise Variance Equalization (NVE)

**Photon noise.** Counts follow **Poisson** → mean = variance ∝ intensity; plus constant read noise.  
**Goal.** Make variance **independent of intensity**. For variance \(\sigma_g^2 \propto g\) and monotone \(g' = f(g)\): choose \(f\) so that \(\sigma_{g'}^2\) is constant.  
**Derivation (sketch).** Variance propagation → \( f'(g) \propto \frac{1}{\sqrt{g}} \) ⇒  
\[ g' = c\,\sqrt{g} + d \]  
(**Anscombe-like** transform). Dark regions are stretched; bright compressed.

*Suggested figure:* effect of \(\sqrt{\cdot}\) mapping on a gradient.

## 4.3 Binary Images & Common Operations

**Binary images.** Pixels in {0,1}. Often obtained via thresholding or segmentation masks.

### Connected components (CC)
- **Neighborhoods:** **N4** (up/down/left/right) vs **N8** (plus diagonals).  
- **Labeling (grid exploit):** single pass creates provisional labels using processed neighbors; equivalence table resolves merges; second pass compacts labels.  
- **Complexity:** linear in number of foreground pixels.

### Distance transform (DT)
- **Task:** distance of each pixel to nearest border.  
- **Two-pass scheme:** TL→BR then BR→TL, keep min; versions for N4 and N8 (under/over-estimate Euclidean).  
- **Better approx:** combine straight + diagonal costs; **EDT** (exact) available in libraries.

### Morphology
- **Erosion:** shrinks foreground; removes outliers.  
- **Dilation:** expands foreground; fills holes.  
- **Opening = erosion→dilation:** removes small specks.  
- **Closing = dilation→erosion:** fills small holes.  
- Useful to **clean masks** before CC/DT.

*Suggested figures:* CC labeling example; N4 vs N8 DT; opening/closing before-after.

---

# Chapter 5 — Local Operators, Geometric Transforms & Cross Correlation

> Local (neighborhood) operators via **convolution**, **geometric image warps + resampling**, and **template matching** with **normalized cross correlation (NCC)**.

## 5.1 Local Operators via Convolution — Smoothing & Gradients

**Operator types.** Point (per-pixel), **local** (neighborhood), global. Point ops struggle with noise/local structure; **local** ops aggregate neighbors.

**Convolution.** Discrete 2D: \((f * k)[i,j] = \sum_u \sum_v f[i-u, j-v]\,k[u,v]\). Properties: **commutative**, **associative**, **distributive**.  
**Separable kernels.** \(k(x,y)=k_x(x)k_y(y)\) → two 1D passes (faster).

**Box filter.** Mean over a window (kernel sums to **1**); reduces noise; border handling: constant, wrap, clamp, mirror.  
**Median filter.** Robust to outliers (nonlinear).

**Binomial/Gaussian smoothing.** Binomial weights (Pascal triangle) ≈ discrete Gaussian; gentler smoothing than box for same window.  
**Integral image.** Prefix sums for O(1) box sums per window.

**Gradients (1st derivatives).** Weighted differences with zero-sum kernels; in 2D: \(\nabla I = (I_x, I_y)\), magnitude \(|\nabla I|\), direction \(\theta\).  
**Sobel (3×3)** = smoothing × derivative; **Scharr** improves gradient **direction** accuracy.  
**2nd derivatives.** 1D \(f''\) via [1, −2, 1]; **Laplacian** \(\nabla^2 I = I_{xx}+I_{yy}\) for edges.

*Suggested figures:* kernel examples (box/binomial), Sobel masks, gradient magnitude image.

## 5.2 Geometric Transformations & Resampling

**Transforms.** Map output coords \(x'\) to input \(x\) via **inverse warping**: \(x = T^{-1}(x')\). Examples: translation, scale, rotation, affine; rectification, registration, texture mapping.

**Interpolation.** Assign intensity at non-integer \(x\):  
- **Nearest neighbor** (fast, blocky), **bilinear** (balanced), **bicubic** (smooth, costlier).  
- **Quality vs speed:** NN (++) / (−); Bilinear (+) / (0); Bicubic (−) / (++).

**Forward vs inverse warping.** Forward leaves **holes**; **always prefer inverse** (then interpolate).

**Subsampling (downscale).** Naive decimation → **aliasing**. Pre-filter with **binomial/Gaussian** before taking every nth pixel.  
**Kernel width & scale.** Smoothing strength depends on kernel std and local scale **m** of transform.  
**Image pyramids.** Successively downsampled images (×1/2 per level) for multiscale processing.

*Suggested figures:* bilinear vs bicubic; forward vs inverse warping; pyramid illustration.

## 5.3 Template Matching with Normalized Cross Correlation (NCC)

**Goal.** Find template \(g_2\) in image \(g_1\) under **translation**, allowing brightness/contrast changes.  
**NCC** at offset \(\Delta\):  
\[ \rho(\Delta) = \frac{\sum (g_1 - \mu_1)(g_2 - \mu_2)}{\sigma_1\,\sigma_2} \]  
computed over the overlap region. Maximize \(\rho\) → best match (\(\rho\in[-1,1]\)).

**Search.** Exhaustive over \(\Delta x,\Delta y\) or **coarse-to-fine** using an **image pyramid**.  
**Limitations.** Assumes translation only, uniform noise, no occlusion; quality degrades with rotation > ~20° or scale > ~30%.

**Subpixel refinement.** Fit a **quadratic** to the NCC peak neighborhood; use gradient/Hessian to estimate argmax → ~0.1 px precision.

*Suggested figures:* NCC heatmap with peak; pyramid search schematic.



---

# Chapter 6 — Visual Features: Keypoints & Descriptors

> Find **distinct points** (keypoints) and describe their **local appearance** for matching across images.

## 6.1 Keypoints (Corners & Blobs)

**Idea.** Corners/texture-rich spots are stable under translation/rotation/illumination and good for matching.

**Structure matrix (second-moment).** From image gradients \(I_x, I_y\) (e.g., Sobel/Scharr), build
\[ M = \sum_{(u,v)\in\mathcal{N}} \begin{bmatrix} I_x^2 & I_x I_y \\ I_x I_y & I_y^2 \end{bmatrix}. \]
- **Harris**: \(R = \det(M) - k\,\mathrm{trace}(M)^2\); large \(R\) ⇢ corner.  
- **Shi–Tomasi**: threshold the **smallest eigenvalue** \(\lambda_{\min}\).  
- **Förstner**: criteria on size/roundness of error ellipse; supports **subpixel** refinement.

**Practical steps.** Gray → smooth → gradients → **corner response** → **threshold** → **non-maximum suppression**.

**Difference of Gaussians (DoG).** Build a scale-space pyramid; for each level, blur with \(\sigma\), subtract consecutive blurs, find **extrema across scales** (corners/blobs). Suppress edge responses using an eigenvalue ratio test.

*Suggested figure:* Harris/Shi–Tomasi responses with NMS (Figure X).

## 6.2 Descriptors (SIFT, BRIEF, ORB)

**Goal.** Represent a keypoint’s neighborhood with a vector **robust** to viewpoint/illumination.

**SIFT.**
- Keypoint: DoG extremum with **scale** and **orientation**.  
- Descriptor: **16×16** window at keypoint scale → **4×4** cells × **8-orient** hist = **128D** (normalized).  
- Invariance: translation/rotation/scale; partial to lighting and affine.  
- Matching: Euclidean distance + **Lowe’s ratio test**.

**Binary descriptors.** Fast & compact.
- **BRIEF**: compare **intensity pairs** in a smoothed patch → bitstring; compare via **Hamming distance**.  
- **ORB**: BRIEF + **orientation** (via moments) + **learned pair selection**; rotation-invariant in-plane, near real-time.

**Notes.** Keep **consistent pair set & order** for binary descriptors. For scale-invariance with ORB, use an **image pyramid**.

*Suggested figures:* SIFT cell grid and histogram; ORB rotation compensation (Figure X).

---

# Chapter 7 — Image Segmentation with Mean Shift

> **Unsupervised** clustering of pixels in a **joint feature space** (e.g., color + position) by following **density modes**.

**Kernel Density Estimation (KDE).** With kernel \(K\) (often Gaussian) and bandwidths \(h_f, h_s\): estimate local density and compute the **mean shift vector** (center-of-mass shift).

**Algorithm.**
1) Choose kernel & bandwidth(s).  
2) For each pixel feature vector, iteratively **shift window to the local mean** until convergence.  
3) **Merge** windows that converge within thresholds → **regions** (attraction basins).

**Design.**
- Feature space: color (e.g., Lab/RGB), optionally **spatial coords**; bandwidths **K_f** (feature) and **K_s** (spatial) control granularity.  
- Pros: no preset number of clusters; flexible shapes; robust to outliers.  
- Cons: bandwidth selection matters; scales poorly with **high-dimensional** features.

**Tips.** Use **pyramids** or subsampling for speed; post-process with **morphology** and **connected components**.

*Suggested figure:* mean shift trajectories toward modes; segmentation examples (Figure X).


# Chapter 8 — Introduction to Classification

## 8.1 Motivation & Problem Setup
**Goal.** Given features \(x\) and a finite set of classes \(\mathcal{C}\), learn a mapping \(f: \mathbb{R}^d \rightarrow \mathcal{C}\).  
**Examples.** Vegetation/non‑vegetation from pixel spectra; “family car” from price/power.
- **Supervised learning:** labeled pairs \((x_i, y_i)\).
- **Classification vs. regression:** discrete labels vs. continuous outputs.

**Suggested figures**
- Two toy classes in 2‑D feature space with a separating boundary.
- Table contrasting classification vs. regression (outputs, losses, metrics).

## 8.2 Hypotheses, Version Space, and Margin
- **Multiple consistent hypotheses:** same training data, different separators.
- **Version space:** set of hypotheses consistent with the labels.
- **Large margin principle:** prefer separator maximizing distance to support points.

**Suggested figures**
- Most‑general vs. most‑specific hypothesis cones; shaded version space.
- Max‑margin separator with support vectors highlighted.

## 8.3 Errors, Confusion Matrix, and Metrics
Let TP, FP, TN, FN denote counts.
- **Precision:** \(\text{Prec}=\frac{\mathrm{TP}}{\mathrm{TP}+\mathrm{FP}}\)  
- **Recall / TPR / Sensitivity:** \(\text{Rec}=\frac{\mathrm{TP}}{\mathrm{TP}+\mathrm{FN}}\)  
- **Specificity / TNR:** \(\frac{\mathrm{TN}}{\mathrm{TN}+\mathrm{FP}}\)  
- **F1:** \(2\frac{\text{Prec}\cdot \text{Rec}}{\text{Prec}+\text{Rec}}\)  
- **ROC curve:** TPR vs. FPR for varying thresholds.
- **PR curve:** Precision vs. Recall.

**Suggested figures**
- Confusion matrix heatmap.
- ROC and PR curves for three competing methods.

## 8.4 Generalization: Bias–Variance
- **Bias:** error from restrictive assumptions (underfitting).  
- **Variance:** sensitivity to data fluctuations (overfitting).  
- **Trade‑off:** \(\mathbb{E}[\mathrm{MSE}] = \sigma_{\text{noise}}^2 + \text{bias}^2 + \text{variance}\).
- **Rules of thumb:** start simple, add model capacity with more data; spend effort on features.

**Suggested figures**
- U‑shaped validation curve vs. model capacity.
- Training vs. validation error across epochs/capacity.

## 8.5 Feature Design
- **Per‑pixel:** intensity, RGB, multispectral/hyperspectral signatures.
- **Neighborhood:** patch/region statistics → higher‑dimensional features.
- **Task‑specific:** texture (LBP), gradients (HOG), etc.

**Suggested figures**
- Pixel vs. patch feature visualization on a remote‑sensing tile.
- Hyperspectral signature plot for two classes.

## 8.6 Nearest Neighbor (NN/k‑NN)
- **NN:** Voronoi partition; sensitive to noise/scale.  
- **k‑NN:** majority/weighted vote among \(k\) nearest; \(k\) tunes bias–variance.
- **Distance metrics:** Euclidean, cosine, Mahalanobis (handle class variance).

**Suggested figures**
- Decision regions for NN vs. k‑NN on toy data.
- Effect of \(k\) on boundary smoothness.

## 8.7 Decision Trees
- **Split nodes:** choose tests that maximize class purity (entropy or Gini).  
- **Leaves:** posterior class distribution or label.  
- **Stopping/pruning:** avoid overfitting.

**Suggested figures**
- Tiny tree with axis‑aligned thresholds.  
- Entropy/Gini impurity vs. split threshold plot.

## 8.8 Support Vector Machines (SVMs)
- **Hard/soft margin:** maximize margin with slack \(\xi_i\).  
- **Kernel trick:** linear separator in feature space via kernels (RBF, poly).  
- **Decision:** sign of \(w^\top x + b\).

**Suggested figures**
- Max‑margin geometry with margin planes \(H_1,H_2\).  
- Effect of RBF kernel width on decision boundary.

## 8.9 MAP Classification (Bayes)
- **Bayes rule:** \(p(c\mid x)\propto p(x\mid c)p(c)\).  
- **MAP decision:** \(\arg\max_c\, p(x\mid c)p(c)\).  
- **Loss/risk:** incorporate class‑dependent penalties; optional reject class.

**Suggested figures**
- Gaussian class‑conditional contours with MAP boundary.  
- Risk surface for asymmetric losses.

## 8.10 Ensembles: Bagging, Random Forests, Boosting (AdaBoost)
- **Bagging:** resample data, average/vote to reduce variance.  
- **Random Forests:** bagging + random feature subsets per split.  
- **Boosting (AdaBoost):** sequentially reweight errors; combine weak learners.

**Suggested figures**
- Pipeline sketches for bagging vs. boosting.  
- Decision‑stump boundaries through boosting rounds; margin growth histogram.

---

# Chapter 9 — Neural Networks (MLPs) & Learning

## 9.1 From Perceptron to MLP
- **Neuron:** \(z=w^\top a + b\), activation \(a'=\phi(z)\) (ReLU, sigmoid, tanh).  
- **MLP:** stacked affine+nonlinear layers; universal approximation.

**Suggested figures**
- Single neuron diagram with weights, bias, activation.  
- MLP block: input → hidden layers → output.

## 9.2 Inputs, Outputs, and Encodings
- **Images:** vectorize pixels (or keep tensors for CNNs).  
- **Labels:** one‑hot vectors; softmax outputs for multi‑class.

**Suggested figures**
- MNIST “5” with one‑hot target.  
- Softmax bar plot for a sample prediction.

## 9.3 Forward Pass in Matrix Form
Layer \(l\): \(z^{(l)}=W^{(l)}a^{(l-1)}+b^{(l)}\), \(a^{(l)}=\phi^{(l)}(z^{(l)})\).  
Inference is linear algebra with nonlinearities between layers.

**Suggested figures**
- Computational graph of a 3‑layer MLP with tensors annotated.

## 9.4 Losses and Optimization
- **Losses:** MSE, cross‑entropy.  
- **Gradient Descent/SGD:** \(\theta\leftarrow \theta-\eta\nabla_\theta \mathcal{L}\).  
- **Mini‑batches:** stochastic approximation for scalability.  
- **Optimizers:** momentum, Adam; LR schedules; early stopping; weight decay.

**Suggested figures**
- Loss landscape slice with GD steps.  
- Training/validation curves comparing SGD vs. Adam.

## 9.5 Backpropagation
- **Key ideas:** computational graph, chain rule, local gradients.  
- **Forward:** cache intermediates; **backward:** propagate sensitivities to obtain \(\nabla_\theta \mathcal{L}\).

**Suggested figures**
- Tiny graph with forward values and backprop derivatives.  
- Table of layerwise gradient shapes.

## 9.6 Practicalities
- **Initialization:** He/Xavier.  
- **Activation choice:** ReLU variants; batch/Layer/Instance Norm.  
- **Regularization:** dropout, data augmentation, early stopping.

**Suggested figures**
- Effect of batch norm on training speed.  
- Overfitting example reduced by dropout.

---

# Chapter 10 — Convolutional Neural Networks (CNNs)

## 10.1 Why CNNs?
- Preserve spatial structure; learn **local**, translation‑equivariant features.  
- Convolutions + pooling/strides reduce resolution and increase receptive field.

**Suggested figures**
- “Flatten vs. keep tensor” comparison.  
- Receptive field growth across layers.

## 10.2 Convolution Layers
- **Input tensor:** \(C_\text{in}\times H\times W\).  
- **Kernels:** \(C_\text{out}\) filters of shape \(C_\text{in}\times k\times k\).  
- **Padding/stride:** control output size; “same” vs. “valid”.

**Suggested figures**
- Sliding kernel animation (one channel for clarity).  
- Output shape formula diagram with \(k,s,p\).

## 10.3 Pooling and Striding
- **Max/Average pooling:** downsample; translation robustness.  
- **Strided convs:** alternative to pooling.

**Suggested figures**
- 2×2 max‑pool example with stride 2.  
- Strided conv vs. pooling comparison grid.

## 10.4 Architectures & Normalization
- **LeNet‑5 → AlexNet → VGG → ResNet:** increasing depth, residuals.  
- **Normalization:** batch norm to stabilize and speed training.

**Suggested figures**
- Block diagrams of LeNet‑5 and a ResNet residual block.  
- Training curves with/without normalization.

## 10.5 Training CNNs
- **Loss/opt:** as in MLPs; often larger batches; strong augmentation.  
- **Regularization:** weight decay, dropout (often later layers).

**Suggested figures**
- Data augmentation gallery.  
- Accuracy vs. epoch for baseline vs. augmented.

---

# Chapter 11 — Camera Parameters, DLT, Calibration & P3P

## 11.1 Coordinate Systems and the Imaging Chain
- **Spaces:** world/object \((\mathcal{W})\), camera \((\mathcal{C})\), image plane \((\mathcal{I})\), sensor \((\mathcal{S})\).  
- **Pipeline:** world \(\to\) camera (extrinsics) \(\to\) ideal projection \(\to\) sensor mapping \(\to\) non‑linear distortions.

**Suggested figures**
- Four coordinate frames sketch with arrows.  
- Pinhole camera geometry with principal point and focal length \(c\).

## 11.2 Extrinsics (Pose)
- **Rigid transform:** \(X_c = RX_w + t\).  
- **Homogeneous form:** \(\begin{bmatrix}X_c\\1\end{bmatrix}=\begin{bmatrix}R&t\\0&1\end{bmatrix}\begin{bmatrix}X_w\\1\end{bmatrix}\).

**Suggested figures**
- Rotation+translation block matrix with axes annotations.  
- Pose visualization relative to a calibration rig.

## 11.3 Intrinsics (Ideal & Affine Camera)
- **Ideal pinhole:** \(x_i = \Pi(X_c)=\big[\frac{cX_c}{Z_c},\frac{cY_c}{Z_c},1\big]^\top\).  
- **Affine/sensor mapping:** principal point \((x_H, y_H)\), pixel scales \(m_x,m_y\), shear \(s\).  
- **Calibration matrix \(K\):**  
\(
K=\begin{bmatrix}
\alpha_x & s & x_H\\
0 & \alpha_y & y_H\\
0&0&1
\end{bmatrix},
\quad \alpha_x = c\,m_x,\ \alpha_y=c\,m_y
\).

**Suggested figures**
- From image plane to pixel coordinates with shift/scale/shear.  
- Matrix \(K\) labeled by parameters.

## 11.4 Full Projection & DLT
- **Projection matrix:** \(P=K\,[R\ |\ t]\in \mathbb{R}^{3\times 4}\).  
- **Homog. mapping:** \(x \sim PX\).  
- **DLT (uncalibrated):** stack linear equations from \((X_i,x_i)\) pairs, solve \(Mp=0\) via SVD for \(p=\mathrm{vec}(P)\).  
  - Needs \(\ge 6\) non‑coplanar points; rank issues on critical surfaces (coplanar points).

**Suggested figures**
- Equation stacking for DLT with a small numeric toy example.  
- Failure case when all control points lie on a plane.

## 11.5 Non‑Linear Distortions
- **Radial distortion:** \(x_d = x(1+k_1 r^2 + k_2 r^4 + \cdots)\), \(r=\|x - x_H\|\).  
- **Tangential, thin‑prism…** handled by additional parameters; invert via iteration.

**Suggested figures**
- Barrel/pincushion grid before/after rectification.  
- Iterative undistortion schematic (initial guess, update loop).

## 11.6 Calibration Methods
### 11.6.1 DLT‑based Full Calibration
- Solve \(P\) from \(\ge6\) 3D–2D correspondences, then decompose \(P\to K,R,t\) via **RQ** (or QR on \(P_{1:3,1:3}^{-1}\)).

**Suggested figures**
- Block showing SVD → \(P\), then RQ → \(K,R\), then \(t\).

### 11.6.2 Zhang’s Checkerboard Method (Intrinsic‑only first)
- For each image, estimate homography \(H\) (planar board: \(Z=0\)).  
- Use constraints on \(H\) to solve a linear system for \(B=K^{-\top}K^{-1}\), then **Cholesky** to recover \(K\).  
- Refine intrinsics + distortion with non‑linear least squares (LM).

**Suggested figures**
- Checkerboard with board/world frame alignment.  
- Matrix flow: points → \(H\) → \(Vb=0\) → \(B\) → \(K\) → non‑linear refinement.

## 11.7 P3P / Spatial Resection (Calibrated)
- **Given:** intrinsics \(K\), three 3D points \(X_i\) and their image bearings \(u_i\).  
- **Step 1 (rays):** compute unit rays \(d_i = \frac{K^{-1}\tilde{x}_i}{\|K^{-1}\tilde{x}_i\|}\).  
- **Step 2 (distances):** apply cosine law on triangle formed by \(X_i\) to solve quartic for depths \(\lambda_i\) (up to 4 solutions).  
- **Step 3 (disambiguation):** use a 4th point or prior to pick the valid solution.  
- **Step 4 (pose):** align \(\{ \lambda_i d_i\}\) to \(\{X_i\}\) via Procrustes/Umeyama to get \(R,t\).  
- **RANSAC:** robustify over many correspondences.

**Suggested figures**
- Geometry of three rays to three control points.  
- Quartic solutions plot; selection with a 4th correspondence.  
- Pose alignment diagram (camera frame ↔ world frame).

## 11.8 Camera Classes & Parameter Counts
- Unit → ideal → Euclidean → affine → general (non‑linear).  
- Parameter counts: \(6\) (pose) + \(5\) (linear intrinsics) + distortion params \(N\).

**Suggested figures**
- Hierarchy ladder diagram with sample matrices and parameter numbers.

---

# References (selected)
- Alpaydin, *Introduction to Machine Learning*.
- Goodfellow, Bengio, Courville, *Deep Learning*.
- Nielsen, *Neural Networks and Deep Learning* (online book).
- Förstner & Wrobel, *Photogrammetric Computer Vision*.
- Zhang, “A Flexible New Technique for Camera Calibration,” MSR‑TR‑98‑71.

























---


Blank TK 


---



# Chapter 8 — Introduction to Classification

**Task:** learn \(f: \mathbb{R}^d \to \{1..C\}\) mapping features \(x\) to class \(y\).

**Supervised setup:** labeled pairs \((x_i, y_i)\). Two outputs: class label (classification) vs. real value (regression).

**Evaluation:**
- Confusion matrix: TP, FP, TN, FN.
- Precision = TP/(TP+FP); Recall (TPR) = TP/(TP+FN); F1 = 2·(P·R)/(P+R).
- ROC: plot TPR vs. FPR; PR curves for class imbalance.

**Generalization & errors:** bias–variance trade‑off; under/overfitting. Use simple models first, grow with data; good features help.

**Validation:** 5×2 CV (or K‑fold).

**Features:** pixel/ RGB/ hyperspectral; patch/region statistics; higher‑D neighborhoods.

**Nearest Neighbor:**
- NN: assign label of closest train point (Euclidean). k‑NN: majority of k nearest; larger k ⇒ smoother, risk of bias.
- Pros: simple, non‑parametric. Cons: sensitive to noise/scale; dense data needed.

**Decision Trees:**
- Split space with axis‑aligned tests until leaves. Choose split minimizing impurity (Entropy or Gini).
- Stop early / prune to avoid overfit. Leaves store posteriors.

**Support Vector Machines (linear):**
- Maximize margin between classes; convex QP. Soft margin with slack; kernels map to higher‑D.

**MAP (Bayes) classification:**
- \(\hat y = \arg\max_y p(y\mid x) \propto p(x\mid y) p(y)\). 
- Loss‑aware decisions: minimize risk; allow “reject” when posterior is low.

**Ensembles:**
- Bagging/Random Forests: bootstrap data + feature subsampling; vote across trees.
- Boosting (AdaBoost): reweight mistakes; strong classifier \(F(x)=\sum_t \alpha_t h_t(x)\).

**Rules of thumb:** start simple; cross‑validate; monitor precision/recall (task‑dependent); beware data leakage.

---

# Chapter 9 — Neural Networks

**MLP basics:** networks are functions composed of affine transforms + nonlinearities.
- Neuron: \(a = \sigma(w^\top x + b)\), common \(\sigma\): ReLU, sigmoid, tanh.
- Layers via matrices; feedforward from input to output.

**Outputs:** multi‑class with softmax; vector of class scores/probabilities.

**Training:** choose loss (e.g., cross‑entropy); minimize over weights/biases.
- Gradient Descent / SGD with mini‑batches.
- Backpropagation = chain rule on computation graph to get \(\nabla_\theta L\).
- Many params ⇒ need regularization (weight decay, dropout), normalization (batch/layer), and good initialization.

**CNN motivation:** preserve spatial structure; learn local features.
- Convolution layers: kernels (filters) slide over H×W×C; padding/stride control size.
- Stacking conv + nonlinearity (+ normalization) + pooling grows receptive field.
- Tensors: input \(N\times C\times H\times W\); conv weights \(F\times C\times K\times K\); output \(N\times F\times H'\times W'\).

**Pooling & stride:** downsample (max/avg). Use bilinear upsampling or transposed conv for increasing resolution.

**Architectures (historical):** LeNet‑5 → AlexNet → VGG → ResNet (residual blocks enable depth).

**Optimization tips:** learning rate schedules, momentum/Adam, data augmentation, early stopping, proper train/val/test splits.

**End‑to‑end:** CNNs learn features + classifier jointly; standard for vision tasks (classification, segmentation, detection).

**Key equations:** 
- Cross‑entropy (softmax): \(L = -\sum_c y_c \log \hat p_c\).
- SGD step: \(\theta \leftarrow \theta - \eta\, \nabla_\theta L_{\text{mini-batch}}\).

**Takeaways:** choose model by data & constraints; monitor both accuracy and calibration; for small data prefer simpler models or transfer learning.



# Chapter 10 — Math Basics & Homogeneous Transforms

> Brief, informal refresher of math tools used later: solving linear systems, least‑squares & Gauss–Newton, SVD/null spaces, skew‑symmetric matrices, rotation derivatives, and homogeneous coordinates & transforms.

## 10.1 Solving Linear Systems \(Ax=b\)

### Cases
- **Square full-rank** \(A\in\mathbb{R}^{n\times n}\): unique solution \(x=A^{-1}b\) (practically: Gaussian elimination / Cholesky for SPD / QR / conjugate gradients).
- **Overdetermined** \(m>n\): minimize \(\|Ax-b\|_2^2\) → normal equations \(A^\top A x=A^\top b\) or use QR/SVD.
- **Underdetermined** \(m<n\): infinite solutions; choose **minimum‑norm** solution \(x=A^\dagger b\) via SVD pseudoinverse.

### Pseudoinverse via SVD
Let \(A=U\,\Sigma\,V^\top\), \(\Sigma=\mathrm{diag}(\sigma_1\ge\cdots\ge\sigma_r>0)\).
- **Least‑squares**: \(x^\star=A^\dagger b=V\,\Sigma^\dagger U^\top b\) with \(\Sigma^\dagger=\mathrm{diag}(\sigma_i^{-1})\).
- **Null space**: last right singular vector \(v_{min}\) minimizes \(\|Ax\|\) with \(\|x\|=1\).

## 10.2 Homogeneous System \(Ax=0\)

- Nontrivial solutions exist iff \(\mathrm{rank}(A)<n\).
- Using SVD \(A=U\Sigma V^\top\): **solution** \(x = v_{min}\) (column of \(V\) for smallest singular value).

## 10.3 Least Squares & Gauss–Newton

Given residuals \(e_i(x)=z_i-h_i(x)\) with information \( \Omega_i \), minimize
\[\min_x \sum_i e_i(x)^\top \Omega_i\, e_i(x).\]

Linearize around \(x\) with Jacobian \(J_i=\partial e_i/\partial x\):
\[\; e_i(x+\Delta)\approx e_i(x)+J_i\,\Delta.\]

Assemble normal equations:
\[\underbrace{\sum_i J_i^\top \Omega_i J_i}_{H}\,\Delta = \underbrace{-\sum_i J_i^\top \Omega_i e_i}_{g}.\]

Iterate: solve \(H\Delta=g\), update \(x\leftarrow x+\Delta\).

## 10.4 Skew‑Symmetric Matrices & Cross Product

A matrix \(S\) is **skew‑symmetric** if \(S^\top=-S\). In \(\mathbb{R}^3\), for \(a=[a_x,a_y,a_z]^\top\):
\[[a]_\times=\begin{bmatrix}0&-a_z&a_y\\ a_z&0&-a_x\\ -a_y&a_x&0\end{bmatrix},\quad [a]_\times b = a\times b.\]

Useful identities:
- \([R a]_\times = R[a]_\times R^\top\) for \(R\in SO(3)\).
- \([a]_\times[a]_\times = a a^\top - \|a\|^2 I\).

## 10.5 Derivative of Rotation Matrices

For a time‑varying rotation \(R(t)\in SO(3)\), there exists an angular‑velocity vector \(\omega(t)\) s.t.
\[\dot R = [\omega]_\times R,\qquad R^\top \dot R = [\omega]_\times.\]

Special case: rotation about fixed axis \(\hat u\) with angle \(\theta(t)\): \(\omega=\dot\theta\,\hat u\).

Infinitesimal rotation: \(R(\delta\theta,\hat u)\approx I + [\delta\theta\,\hat u]_\times\).

## 10.6 Homogeneous Coordinates & Projective Geometry

Homogeneous point in 2D: \(p=\begin{bmatrix}x\\y\\w\end{bmatrix}\sim\lambda p\) \((\lambda\neq 0)\).
- Euclidean recovery: \( (x/w,\;y/w) \) for \(w\neq 0\); **points at infinity** have \(w=0\).  
3D analog: \(P=[x,y,z,w]^\top\).

### Lines, planes, and incidence
- 2D line \(\ell=[a,b,c]^\top\) represents set \(\{p\mid \ell^\top p=0\}\).
- Point on line: \(\ell^\top p=0\). Intersection of lines: \(p=\ell_1 \times \ell_2\).  
- Line through points: \(\ell = p_1 \times p_2\).  
3D: plane \(\pi\) and point \(P\) satisfy \(\pi^\top P=0\).

### Why homogeneous?
- Compact formulas, **points at infinity**, and **matrix‑only** transforms; straight lines map to straight lines.

## 10.7 Homogeneous Transforms (3D)

General projective transform \(H\in\mathbb{R}^{4\times 4}\), \(P' \sim H P\). Important subclasses:

- **Translation** \(t\in\mathbb{R}^3\): \(\begin{bmatrix}R&I,t\\0&1\end{bmatrix}\) with \(R=I\).
- **Rotation** \(R\in SO(3)\): \(\begin{bmatrix}R&0\\0&1\end{bmatrix}\).
- **Rigid (SE(3))**: \(T=\begin{bmatrix}R&t\\0&1\end{bmatrix}\) (6 dof).
- **Similarity (Sim(3))**: \(s>0\): \(\begin{bmatrix}sR&t\\0&1\end{bmatrix}\) (7 dof).
- **Affine**: last row \([0,0,0,1]\) but arbitrary linear part (12 dof).
- **Projective**: full \(4\times4\) up to scale (15 dof total).

Composition: \(T_{ac}=T_{ab}\,T_{bc}\) (note non‑commutativity). Inverse of rigid:
\[T^{-1}=\begin{bmatrix}R^\top&-R^\top t\\0&1\end{bmatrix}.\]

## 10.8 Camera & Projective Effects (intuition)

- Pinhole cameras preserve lines, **not** lengths/angles; parallel world lines meet at **vanishing points** (points at infinity).
- Homogeneous coordinates model those effects naturally.

## 10.9 Resampling & Intersections in 2D (quick tools)

- Intersection of two image lines \(\ell_1,\ell_2\): \(p=\ell_1\times \ell_2\) → dehomogenize.
- Line through two pixels \(p_1,p_2\): \(\ell=p_1\times p_2\).
- Point on line test: \(|\ell^\top p|\) (use threshold in pixels).

---

**Key Takeaways**  
- Use **SVD** for \(Ax=0\) and robust LS.  
- **Gauss–Newton** solves non‑linear LS via iterative linearization.  
- \([\cdot]_\times\) turns cross products and angular velocity into matrices.  
- \(\dot R=[\omega]_\times R\) is the fundamental rotation derivative.  
- Homogeneous coordinates enable elegant geometry & chaining of 3D transforms.



# Chapter 11 — Camera Parameters, Calibration, and P3P

> Based on course notes by **Cyrill Stachniss**. This chapter summarizes camera **extrinsics & intrinsics**, **DLT**, **Zhang’s checkerboard calibration**, and **P3P / spatial resection**.

---

## 11.1 Coordinate Systems & Mapping Overview

**Goal:** map a 3D world point to a 2D pixel on the sensor.

Coordinate systems:
1. **World/Object**: $\mathbf{X} = (X,Y,Z)^T$  
2. **Camera**: $\mathbf{X}_c = (X_c,Y_c,Z_c)^T$  
3. **Image plane** (ideal pinhole)  
4. **Sensor** (pixels)

Overall pipeline:

- **Extrinsics** (world $\to$ camera): rigid transform $(\mathbf{R},\,\mathbf{t})$  
- **Ideal perspective projection** (camera $\to$ image plane; focal length $c$)  
- **Affine mapping to sensor** (principal point, unequal scales, shear)  
- **Nonlinear corrections** (e.g., lens distortion)

In homogeneous coordinates:

$$
\lambda\,\mathbf{x} \;=\; \mathbf{K}\,[\,\mathbf{R}\mid\mathbf{t}\,]\;\mathbf{X}_h, 
\quad \mathbf{X}_h=(X,Y,Z,1)^T,\;\mathbf{x}=(x,y,1)^T
$$

---

## 11.2 Extrinsic Parameters (Pose)

Pose = position + orientation of camera w.r.t. world:

$$
\mathbf{X}_c \;=\; \mathbf{R}\,(\mathbf{X}-\mathbf{C}) \;=\; \mathbf{R}\,\mathbf{X} + \mathbf{t},
\quad \mathbf{t} = -\mathbf{R}\,\mathbf{C}
$$

Homogeneous form:
$$
\mathbf{T}_{wc\to c}=\begin{bmatrix}\mathbf{R}&\mathbf{t}\\ \mathbf{0}^T&1\end{bmatrix},\qquad
\mathbf{X}_{c,h} = \mathbf{T}_{wc\to c}\,\mathbf{X}_h
$$

6 DoF total (3 for $\mathbf{C}$, 3 for $\mathbf{R}$).

---

## 11.3 Intrinsic Parameters (Calibration Matrix)

**Ideal perspective projection** (pinhole; image plane $Z=c$):

$$
x_i = -c\,\frac{X_c}{Z_c},\qquad y_i = -c\,\frac{Y_c}{Z_c}
$$

**Affine mapping to sensor** (principal point $(x_H,y_H)$, unequal scales $m_x,m_y$, shear $s$):

$$
\mathbf{K} = 
\begin{bmatrix}
c & s & x_H\\
0 & m\,c & y_H\\
0 & 0 & 1
\end{bmatrix}
\quad\text{(one common parameterization; $m$ encodes scale ratio)}
$$

Complete (linear) imaging model (a.k.a. **DLT** affine camera after projection):

$$
\lambda\,\mathbf{x} \;=\; \mathbf{K}\,[\,\mathbf{R}\mid\mathbf{t}\,]\;\mathbf{X}_h \;\equiv\; \mathbf{P}\,\mathbf{X}_h,
\quad \mathbf{P}\in\mathbb{R}^{3\times 4}
$$

- $\mathbf{P}$ has 11 DoF (up to a scale).  
- Linear errors = 5 intrinsics ($c,s,m,x_H,y_H$) + 6 extrinsics.

**Nonlinear effects**: lens distortions (e.g., radial barrel/pincushion). A standard radial model:

$$
\begin{aligned}
r^2 &= (x - x_H)^2 + (y - y_H)^2,\\
x_d &= x\,(1 + k_1 r^2 + k_2 r^4 + k_3 r^6) + \dots\\
y_d &= y\,(1 + k_1 r^2 + k_2 r^4 + k_3 r^6) + \dots
\end{aligned}
$$

Undistortion generally requires **iteration**.

---

## 11.4 Direct Linear Transform (DLT)

**Task:** Estimate $\mathbf{P}$ (11 parameters up to scale) from $\ge 6$ known 3D–2D correspondences.  
For each correspondence $\mathbf{X}_h \leftrightarrow (x,y)$, derive 2 linear equations in the entries of $\mathbf{P}$. Stack into $\mathbf{M}\,\mathbf{p}=\mathbf{0}$ with $\mathbf{p}=\mathrm{vec}(\mathbf{P})$.

**Solve:** SVD of $\mathbf{M}$, pick the right singular vector for smallest singular value $\rightarrow \hat{\mathbf{p}}$ (up to scale).

**Recover** $(\mathbf{K},\mathbf{R},\mathbf{t})$ from $\mathbf{P}$ by **RQ (or QR) factorization** of the left $3\times3$ block and sign conventions (normalize so that $\mathbf{K}$ has positive diagonal; optionally enforce negative pinhole $c$ by flipping signs consistently).

**Critical configuration:** all control points coplanar $\Rightarrow$ rank deficiency. Prefer spatially diverse points.

---

## 11.5 Zhang’s Checkerboard Calibration (Intrinsics Only)

**Goal:** estimate only the **5 linear intrinsics** $\mathbf{K}$ using multiple views of a **planar** checkerboard of known square size.

- Set the world frame on the checkerboard; all points have $Z=0$.
- For each image, the plane–to–image mapping is a **homography** $\mathbf{H}$ (3×3, 8 DoF up to scale). Estimate $\mathbf{H}$ via DLT from $\ge4$ corner correspondences.
- Use orthonormality constraints of $\mathbf{R}$ (embedded in $\mathbf{H}$) to build a **linear system** $\mathbf{V}\,\mathbf{b}=\mathbf{0}$ in the entries of the **symmetric** matrix $\mathbf{B}=\mathbf{K}^{-T}\mathbf{K}^{-1}$.
- Solve for $\mathbf{b}$ by SVD (last singular vector), reconstruct $\mathbf{B}$, then retrieve $\mathbf{K}$ by **Cholesky** of $\mathbf{B}^{-1}$.

Finally, with $\mathbf{K}$ fixed, estimate per–view extrinsics $(\mathbf{R},\mathbf{t})$, and (optionally) refine **nonlinear/distortion** parameters by minimizing reprojection error with **Levenberg–Marquardt**.

**Minimum:** $\ge 3$ distinct checkerboard poses (each provides two independent equations).

---

## 11.6 Inversion & Rays

Given a pixel $(x_d,y_d)$:
1. **Undistort** iteratively to $(x,y)$ in the ideal image.
2. Back-project a **ray** (not a unique 3D point):
   $$
   \mathbf{d} \propto \mathbf{R}^T\,\mathbf{K}^{-1}\,(x,y,1)^T,\qquad \mathbf{C}=-\mathbf{R}^T\mathbf{t}
   $$

---

## 11.7 P3P / Spatial Resection (Calibrated Camera Pose)

**Problem:** Given 3 known 3D control points and their image measurements (with known $\mathbf{K}$), estimate $(\mathbf{R},\mathbf{t})$ **directly** (no initial guess).

### Step 1 — Distances along rays
- Convert pixels to **unit ray directions** $\hat{\mathbf{u}}_i = \frac{\mathbf{K}^{-1}\tilde{\mathbf{x}}_i}{\|\mathbf{K}^{-1}\tilde{\mathbf{x}}_i\|}$.
- Let unknown distances $d_i$ s.t. camera-frame points $\mathbf{X}_{c,i}=d_i\,\hat{\mathbf{u}}_i$.
- Using known inter-point distances (world) and **law of cosines** via angles between rays, derive a **quartic** in one variable $\Rightarrow$ up to **4 solutions** for $\{d_i\}$.

**Disambiguation:** use a 4th point or prior to select the physically consistent solution.

### Step 2 — Orientation & translation
- With $\mathbf{X}_{c,i}$ and $\mathbf{X}_{w,i}$, compute $(\mathbf{R},\mathbf{t})$ via **absolute orientation** (e.g., Kabsch/Umeyama).

**Robustness:** wrap P3P in **RANSAC** over many minimal triplets; then refine with all inliers via nonlinear least squares.

**Critical cylinder:** certain camera–point configurations yield instability (small angular changes cause large position changes).

---

## 11.8 Classification of Camera Models

- **Unit / normalized camera** (principal point at origin, $c=1$)  
- **Ideal camera** (pinhole, no affine/shear)  
- **Euclidean / affine camera** (with principal point, scales, shear)  
- **General camera** (adds nonlinear distortions)

**Calibrated** = intrinsics known (metric if fixed). **Uncalibrated** = intrinsics unknown.

---

## 11.9 Summary

- Mapping: **extrinsics** + **intrinsics** + **nonlinear** effects.  
- **DLT**: linear estimation of $\mathbf{P}$ from $\ge6$ 3D–2D points; decompose to $(\mathbf{K},\mathbf{R},\mathbf{t})$.  
- **Zhang’s method**: estimate $\mathbf{K}$ from planar homographies; then refine with distortion.  
- **P3P**: minimal calibrated pose from 3 points (quartic; up to 4 solutions); use a 4th point/RANSAC; refine by LS.

**Literature:**  
- Förstner & Wrobel, *Photogrammetric Computer Vision* (Geometry of the Single Image; 11.1–11.1.6).  
- Zhang, “A Flexible New Technique for Camera Calibration,” MSR-TR-98-71.  
- Grunert (1841); Gao (2003); Kneip (2011); Persson “Lambda Twist” (2019); Nakano (2019).





## References
- Förstner & Wrobel, *Photogrammetric Computer Vision*.  
- Förstner, *Photogrammetrie I* (Ch. 2–3, 6).  
- Szeliski, *Computer Vision: Algorithms and Applications* (Ch. 2.2–2.3, 3.3).  
- Stachniss, *Photogrammetry I+II* (Uni Bonn) — slides & videos.













# References
- Förstner & Wrobel, *Photogrammetric Computer Vision*.
- Förstner, *Photogrammetrie I Skriptum*.
- Szeliski, *Computer Vision: Algorithms and Applications* (Springer, 2010).
- Alpaydin, *Introduction to Machine Learning* (2009).
- Hartley & Zisserman, *Multiple View Geometry in Computer Vision* (2004).
- C. Stachniss, *Photogrammetry I + II* (Uni Bonn) — lecture slides & video series.






More resources: https://www.ipb.uni-bonn.de/teaching/index.html



Source of thumbnail image: https://www.slideshare.net/slideshow/introduction-to-simultaneous-localization-and-mapping-slam-a-presentation-from-skydio/242533355


