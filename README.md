# Brain Tumor Segmentation — UNET + ResNet34

A UNET model with a ResNet-34 encoder trained on multi-modal MRI scans to automatically segment brain tumors, achieving an IoU score of 0.65 on the validation set.

**Course:** AI 395T — AI in Healthcare | UT Austin  
**Author:** Vinh Nguyen (VHN354)  
**Date:** December 4, 2024

---

## Overview

Manual brain tumor segmentation from MRI scans is labor-intensive and prone to human error — radiologist error rates remain around 30% per annum, unchanged since 1949. This project automates tumor region detection using a hybrid deep learning architecture combining U-Net's spatial precision with ResNet-34's powerful feature extraction, trained on the BraTS2020 multi-modal MRI dataset.

---

## Architecture

```
Input (4 × 256 × 256)
        ↓
  ResNet-34 Encoder         ← pre-trained ImageNet weights
  (residual skip connections — same block, prevents vanishing gradient)
        ↓
  U-Net Decoder
  (lateral skip connections — encoder ↔ decoder, preserves spatial detail)
        ↓
  Binary Segmentation Mask (1 × 256 × 256)
```

| Component | Role |
|---|---|
| ResNet-34 Encoder | Deep feature extraction via residual (vertical) skip connections |
| U-Net Decoder | Spatial reconstruction via lateral (horizontal) skip connections |
| Combined loss | BCE Loss (pixel-level) + IoU Loss (region-level overlap) |

---

## Dataset

[BraTS2020 — Kaggle](https://www.kaggle.com/datasets/awsaf49/brats20-dataset-training-validation/data)

4 MRI modalities per case: **T1, T1ce, T2, FLAIR** + segmentation mask (`.nii` format).  
Due to compute constraints, only the **median slice (77th)** is used per case.

| Split | Cases |
|---|---|
| Train | 85% of 369 labeled cases |
| Validation | 15% of 369 labeled cases |
| Test | 125 unlabeled cases |

---

## Preprocessing

- Resize: 250×250 → **256×256**
- Stack 4 modalities → input tensor `(4 × 256 × 256)`
- Binary mask → output tensor `(1 × 256 × 256)`

```
Case_001/
├── t1.nii    → slice 77 → (256×256)  ┐
├── t1ce.nii  → slice 77 → (256×256)  ├── stack → (4 × 256 × 256) → Model → (1 × 256 × 256)
├── t2.nii    → slice 77 → (256×256)  │
└── flair.nii → slice 77 → (256×256)  ┘
```

---

## Results

| Set | IoU Score |
|---|---|
| Training | ~0.90 |
| Validation | **0.65** |

Validation IoU of 0.65 is categorized as **Good** performance. The gap vs training (~0.90) reflects mild overfitting, likely due to limited dataset size. Qualitative results on the unlabeled test set show strong tumor region detection visually.

---


