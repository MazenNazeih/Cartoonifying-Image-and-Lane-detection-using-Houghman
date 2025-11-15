# Computer Vision Assignment
## Assignment Overview

This assignment implements two fundamental computer vision techniques:
1. **Image Cartoonization** - Transforming photos into cartoon-style images
2. **Lane Detection** - Detecting road lanes using Hough Transform

---

## Part I: Image Cartoonization

### Objective
Convert a regular photograph into a cartoon-style image with bold outlines and simplified colors.

### Pipeline
```
Original Image
    ├─→ [Sketch Path] Grayscale → Median Filter → Laplacian → Threshold → Black Edges
    └─→ [Color Path] Downscale → 5× Bilateral Filter → Upscale → Flat Colors
                ↓
        Combine Sketch + Colors → Cartoon Output
```

### Key Techniques
- **Median Filter (9×9):** Removes noise while preserving edges
- **Laplacian Edge Detection (5×5):** Detects edges in all directions
- **Inverse Binary Threshold (100):** Creates black edges on white background
- **Bilateral Filter (5 iterations):** Simplifies colors while maintaining edges
  - d=3, sigmaColor=200, sigmaSpace=200

### Output
Clean black outlines with flat, paint-like color regions

---

## Part II: Road Lane Detection

### Objective
Detect and visualize lane markings on road images using the Hough Transform algorithm.

### Pipeline
```
Road Image → Grayscale → Median Filter → Canny Edges 
    → ROI Masking → Hough Transform → Peak Detection 
    → Non-Maximum Suppression → Line Overlay
```

### Key Techniques

#### 1. Preprocessing
- **Median Filter (7×7):** Noise reduction
- **Canny Edge Detection:** Thresholds (50, 150) for strong lane edges

#### 2. Region of Interest (ROI)
- **Trapezoid mask:** Focuses on road area, eliminates sky/sides
- Mimics perspective: wide bottom (near), narrow top (far)

#### 3. Hough Transform
- **Line representation:** x·cos(θ) + y·sin(θ) = ρ
- **Parameter space:** 180 angles (-90° to 90°)
- **Voting:** Each edge pixel votes for all possible lines through it
- **Accumulator:** 2D array storing votes for each (ρ, θ) combination

#### 4. Peak Detection
- **Threshold:** 40% of maximum votes
- **Output:** Top 20 peaks (line candidates)

#### 5. Non-Maximum Suppression
- **Removes duplicates:** Lines within 30 pixels (ρ) and 5° (θ)
- **Greedy algorithm:** Keeps strongest peaks, suppresses nearby weakers
- **Result:** 2-6 distinct lane lines

#### 6. Line Drawing
- **Conversion:** Polar (ρ, θ) → Cartesian (x₁, y₁), (x₂, y₂)
- **Visualization:** Green lines overlaid on original image

### Output
Original image with detected lane markings highlighted in green

---

## Key Parameters

### Part I - Cartoonization
| Parameter | Value | Purpose |
|-----------|-------|---------|
| Median Kernel | 9 | Noise removal |
| Laplacian Kernel | 5 | Edge detection |
| Threshold Value | 100 | Sketch creation |
| Bilateral Iterations | 5 | Color simplification |
| sigmaColor | 200 | Aggressive color smoothing |

### Part II - Lane Detection
| Parameter | Value | Purpose |
|-----------|-------|---------|
| Canny Low | 50 | Weak edge threshold |
| Canny High | 150 | Strong edge threshold |
| Peak Threshold Ratio | 0.4 | 40% of max votes |
| NMS Rho Threshold | 30 | Min pixel distance |
| NMS Theta Threshold | 5 | Min angle bins |
| Max Peaks | 20 | Line candidates |


---

## Key Concepts Learned

### Mathematical Foundations
1. **Second-order derivatives** (Laplacian) for edge detection
2. **Polar line representation** (ρ, θ) for Hough Transform
3. **Parameter space transformation** for robust detection
4. **Non-linear filtering** (median, bilateral) for edge preservation

### Algorithm Design
1. **Multi-stage pipelines** decompose complex tasks
2. **Threshold tuning** critical for performance
3. **Edge preservation** techniques maintain structure
4. **Voting mechanisms** (Hough) aggregate evidence

### Computer Vision Principles
1. **Grayscale conversion** simplifies intensity-based processing
2. **ROI masking** focuses computation on relevant areas
3. **Non-maximum suppression** eliminates redundant detections
4. **Visualization** aids debugging and parameter tuning

---

##  Dependencies

```python
import cv2              # OpenCV for image processing
import numpy as np      # Numerical operations
import matplotlib.pyplot as plt  # Visualization
import pandas as pd  # data processing, CSV file I/O (e.g. pd.read_csv)
```

---


## Tuning Guide

### If Cartoon Has Too Much Noise
- Increase median kernel size (9 → 11)
- Increase Laplacian kernel (5 → 7)
- Increase threshold value (100 → 120)

### If Cartoon Colors Too Simple
- Reduce bilateral iterations (5 → 3)
- Reduce sigmaColor (200 → 150)

### If Lane Detection Misses Lanes
- Lower Canny thresholds (50, 150 → 40, 120)
- Lower peak threshold ratio (0.4 → 0.3)
- Adjust ROI trapezoid vertices

### If Too Many False Lane Detections
- Increase Canny thresholds (50, 150 → 60, 180)
- Increase peak threshold ratio (0.4 → 0.5)
- Tighten NMS thresholds (30, 5 → 20, 3)

---

- Tomasi, C. & Manduchi, R. (1998). "Bilateral Filtering for Gray and Color Images"

---

**Assignment Complete** ✅
