# 🔊 Acoustic SHM Classifier

> **Machine Learning · Spring 2026 · HW3**  
> Structural Health Monitoring of Sandwiched Composite Plates using Percussion Acoustics

**Live App →** [komal-blkmmb.github.io/acoustic-shm-classifier_new](https://komal-blkmmb.github.io/acoustic-shm-classifier_new/)

---

## What This Is

A fully browser-based machine learning pipeline for detecting **delamination defects** in sandwiched composite plates (SCP) using percussion sound recordings. Tap a cell, record the sound — the app classifies it as **healthy or damaged** in seconds.

No Python. No server. No installation. Everything runs locally in your browser using the Web Audio API and pure JavaScript implementations of KNN, Decision Tree, Logistic Regression, and SVM.

---

## The Problem

A 3×9 composite plate (27 cells total) has 8 physically debonded cells:  
`1d · 1e · 1i · 2b · 2g · 3b · 3d · 3g`

Debonded cells produce a **distinctly different acoustic response** when tapped — a duller, shorter ring compared to the crisp resonance of healthy cells. The goal is to automate this detection using ML.

---

## Pipeline Overview

The app walks you through 6 sequential steps:

```
Step 1 → Configure which cells are damaged
Step 2 → Upload recording ZIP files (up to 5 iterations)
Step 3 → Inspect extracted hits visually
Step 4 → Train classifiers (choose any combination)
Step 5 → Test robustness on HW2 data (unseen session)
Step 6 → Classify a single hit in real time
```

---

## Step-by-Step Guide

### Step 1 — Configure Plate Layout
Click cells on the 3×9 grid to toggle them between **healthy** (blue) and **damaged** (red). Default selection matches the 8 physically inspected debonded cells. You can customize for any plate configuration.

### Step 2 — Upload Iteration Data
Upload ZIP files from your recording sessions. Each ZIP should contain 27 audio files named after their cell: `1a.m4a`, `2g.m4a`, `3 b.m4a`, etc. (uppercase, spaces, reversed order all handled automatically).

The app then:
1. Detects each percussion hit using **RMS energy thresholding**
2. Trims the silent tail (cuts when energy drops below 5% of peak)
3. Extracts **154 features** per hit (128 PSD bins + 13 MFCC mean + 13 MFCC std)
4. Normalizes features for cross-session consistency

### Step 3 — Inspect Hits
Select any cell and iteration to visualize all extracted hits overlaid. A consistent waveform shape across hits = good extraction. The cyan mean ± shaded std band shows variability at a glance.

### Step 4 — Train Classifiers
Select any combination of models to train:

| Model | Method | Notes |
|-------|--------|-------|
| **KNN** | k=5 nearest neighbors, Euclidean distance | Non-parametric, good baseline |
| **Decision Tree** | CART, max depth=6, Gini impurity | Interpretable, can overfit |
| **Logistic Regression** | Gradient descent, sigmoid boundary | Linear, fast, generalizes well |
| **SVM** | Pegasos SGD, linear kernel | Good in high-dimensional feature space |

Data is **shuffled and split 70/30** (stratified — class balance preserved in both sets). Features are StandardScaler-normalized on training data only.

Results show training/validation accuracy and confusion matrices for each model.

### Step 5 — Robustness Test
Upload a ZIP of HW2 data (files named like `1a_hit1.wav`, `2g_hit3.wav`). The trained models classify every hit **without any retraining** — this is the true test of generalization. A model that performs well here learned genuine structural damage patterns, not recording-session artefacts.

### Step 6 — Single Hit Classification
Upload any `.wav` or `.m4a` recording of a single tap. All trained models classify it instantly, showing a **GOOD / BAD** verdict per model.

---

## Feature Extraction

Each hit is converted to a **154-dimensional feature vector**:

```
[0   : 127]  →  PSD  — Welch power spectral density (128 log-compressed bins)
[128 : 140]  →  MFCC mean  (13 coefficients, averaged over time)
[141 : 153]  →  MFCC std   (13 coefficients, std deviation over time)
```

**Why PSD?** Healthy and damaged cells resonate at different frequencies. PSD captures how energy is distributed across the frequency spectrum.

**Why MFCC?** MFCCs describe the timbral texture of the sound — the "colour" of the ring-down. Mean and std together describe both average character and how the sound evolves.

**RMS normalization** is applied before feature extraction so that hits recorded at different volumes or microphone distances produce consistent feature vectors.

---

## Audio File Naming

The app handles all real-world naming variations automatically:

| Raw filename | Interpreted as |
|---|---|
| `1a.m4a` | Cell 1a |
| `1E.m4a` | Cell 1e |
| `E1.m4a` | Cell 1e |
| `1 h.m4a` | Cell 1h |
| `3_b.m4a` | Cell 3b |

---

## Browser Compatibility

| Browser | Status |
|---------|--------|
| Chrome / Edge | ✅ Full support (recommended) |
| Firefox | ⚠️ `.m4a` decoding may not work — use `.wav` |
| Safari | ⚠️ Partially supported |

**Use Chrome for best results.** The Web Audio API's `.m4a` decoding relies on the browser's built-in codec support.

---

## Tech Stack

Everything is implemented from scratch in vanilla JavaScript — no ML libraries, no Python backend.

- **Audio decoding** — Web Audio API (`AudioContext.decodeAudioData`)
- **Hit detection** — RMS energy thresholding with minimum gap filtering
- **FFT** — Cooley-Tukey radix-2 in-place algorithm
- **PSD** — Welch's method with Hann windowing
- **MFCC** — Mel filterbank → log energy → DCT-II
- **Classifiers** — KNN, Decision Tree (CART), Logistic Regression, SVM (Pegasos)
- **ZIP handling** — [JSZip](https://stuk.github.io/jszip/)
- **Fonts** — Space Mono + DM Sans (Google Fonts)

---

## Project Context

This app was built for **HW3: KNN, DT, LR, and SVM Models to Detect Delamination** in the Machine Learning course (Spring 2026). The plate used is a sandwiched composite plate (SCP) with 8 physically debonded cells verified by visual inspection.

**Group 23** — Bhupendra Joshi · Saurav Silwal · Saurav Maraseni

---

## Related

- **HW2 Acoustic Signal Analyzer** — Time domain, FFT, PSD, STFT, CWT, MFCC visualization  
  → [komal-blkmmb.github.io/acoustic-shm-classifier](https://komal-blkmmb.github.io/acoustic-shm-classifier/)
