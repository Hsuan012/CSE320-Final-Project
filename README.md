# CSE320-Final-Project
# Lung Lobe Segmentation – Team Project Summary

This README explains:

1. What I implemented  
2. Which models we used  
3. What improvements were added  
4. What results we obtained  
5. How to run everything (including dataset download & GPU setup)  
6. What files matter for our team  

This document is for internal team reference.

---

##  1. Project Goal

We perform **lung lobe segmentation** (LUL, LLL, RUL, RML, RLL) on CT scans from the LOLA11 dataset.  
The main tasks are:

- Run baseline segmentation  
- Compute quantitative metrics (volume, connected components)  
- Apply post-processing cleaning to improve continuity  
- Compare baseline vs cleaned segmentation across **55 cases**  
- Generate plots and visualizations

---

##  2. Models Used

This project uses pretrained models from **lungmask** (no retraining):

###  `R231`
- General lung extraction model  
- Used to get the full lung region

###  `LTRCLobes`
- 5-lobe segmentation model  

###  Combined Pipeline

CT → R231 → LTRCLobes → Baseline Lobe Mask
This is essentially the standard two-stage lungmask pipeline.

---

##  3. Improvements I Implemented

Baseline lobe masks often contain many **fragmented components** (especially RUL/RML).  
To fix this, I built a lobe-wise post-processing pipeline:

###  Remove small isolated fragments
- Each lobe has its own minimum volume threshold  
- RML uses a smaller threshold due to its anatomy

###  Preserve the largest connected component per lobe

###  Safety fallback  
If cleaning increases the number of components, the algorithm automatically **reverts to baseline**.

###  Volume preservation  
- Average volume change between baseline and cleaned is < **1–2%**  
- Ensures no anatomical distortion

###  Visualization outputs
- Baseline overlay  
- Cleaned overlay  
- Difference maps (changed voxels highlighted)  
- Bar charts (avg ± SD)  
- Heatmap showing improvement across 55 cases  

---

##  4. Key Results Summary

###  Improved continuity
- Number of connected components decreased for every lobe  
- Large improvement on heavily fragmented cases  
- Stable improvement on most cases

###  Volume remains stable
- Cleaning removes noise without shrinking lobes  
- Volume difference < 1–2%

###  No case becomes worse
- The fallback mechanism ensures segmentation is **never degraded**

###  Heatmap shows consistent improvements
- Most cases improve moderately  
- A few cases show very large gains  
- No negative improvements

---

##  5. Dataset Preparation (Important)

### **Step 1 — Download the LOLA11 CT dataset**

We use the publicly available LOLA11 dataset.  
Download it from Zenodo:

 **https://zenodo.org/records/4708800**

You will get 55 `.mha` CT files.

---

### **Step 2 — Upload dataset to Google Drive**

Create a folder like:
MyDrive/LOLA11


Place all `.mha` files inside.

---

### **Step 3 — Enable GPU in Colab (required)**

If you do NOT use GPU, the full dataset may take **8+ hours**.  
With GPU, the entire dataset runs in **20–40 minutes**.

To enable GPU:
Runtime → Change runtime type → Hardware accelerator → GPU

Use T4 or L4 if available.

---
