# 👹 The Grumpy Doctogres | AN2DL Challenge 2

**Course:** Artificial Neural Networks and Deep Learning (Politecnico di Milano)

**Team:** The Gradient Descenters

**Status:** Completed (December 16, 2025)

---

## 🧭 Project Overview

Welcome to the **Iron-Guts Hospital**! This project tackles the second challenge of the AN2DL course: a multi-class image classification task to identify molecular subtypes in microscopic human tissue.

The dataset consists of RGB images paired with binary masks highlighting regions of interest. Our goal was to classify each sample into one of four categories, aiding the "orc surgeons" in their prognosis.

### The Challenge

* **Input:** Low-magnification Whole Slide Images (WSI) + Binary Masks.
* **Task:** Classify tissue into `Luminal A`, `Luminal B`, `HER2(+)`, or `Triple negative`.
* **Constraint:** The dataset contained "Shrek" images, duplicates, and artifacts that required rigorous cleaning.

---

## 🧹 Dataset & Cleaning

The original dataset contained 691 training pairs. However, an initial inspection revealed critical quality issues.

### Data Cleaning Pipeline

We implemented a strict cleaning protocol to remove noise:

1. **"Shrek" Removal:** We used MD5 hashing on masks to detect and remove 60 non-informative images containing cartoon characters.
2. **Artifact Removal:** We manually removed 50 duplicated images containing mucosa-like artifacts.
3. **Result:** The final valid training set was reduced to **581 images**.

### Class Distribution

The dataset (after cleaning) remained imbalanced:

* **Luminal B:** ~34.4%
* **HER2(+):** ~29.3%
* **Luminal A:** ~26.2%
* **Triple negative:** ~10.1%

---

## 🧠 Our Solution

Our final solution moved away from standard whole-image classification, which yielded poor results (~25% F1), and instead adopted a **Mask-Guided Patch Classification** strategy.

### 1. Patch Extraction Strategy

Instead of resizing the full image (which loses detail) or using raw masks (which confuses the model with background noise), we extracted patches based on the mask geometry.

* **Method:** For each connected white region in the mask, we extracted a bounding box from the original RGB image.
* **Filtering:** Patches smaller than 200 pixels were discarded to reduce noise.

### 2. Model Architecture

We utilized **Transfer Learning** with a **ConvNeXt-Tiny** backbone.

* **Optimizer:** `Lion` (outperformed AdamW).
* **Loss Function:** Weighted Cross-Entropy (to handle class imbalance).

### 3. Training Strategy: Progressive Unfreezing

To prevent catastrophic forgetting, we fine-tuned the model in stages:

1. **Stage 1:** Train only the classifier head (backbone frozen).
2. **Stage 2:** Unfreeze the last 2 backbone blocks every 20 epochs.
3. **Stage 3:** Full fine-tuning of all blocks.

---

## 📊 Results

The model was evaluated using the macro **F1-Score**.

Our data-centric approach (cleaning + patching) yielded a **17-point improvement** over the baseline.

| Model Configuration | F1 Score (%) |
| --- | --- |
| Baseline CNN (Full Image) | 25.0 |
| + Data Cleaning | 29.0 |
| + Patch Extraction | 32.0 |
| + ConvNeXt-Tiny + Lion | 39.0 |
| + Progressive Unfreezing | 42.31 |
| **+ Patch Filtering (Final)** | **42.84** |

## 🛠️ Repository Structure

```text
├── data/                  # Dataset files (train/test)
├── notebooks/             # Jupyter notebooks for training and analysis
├── models/                # Saved model checkpoints
├── src/                   # Source code for preprocessing and patching
├── FinalReport.pdf        # Detailed project report
└── README.md              # Project documentation

```

---

## 🚀 How to Run

1. **Clone the repository:**
```bash
git clone https://github.com/thegradientdescenters/AN2DL-Challenge-2.git

```


2. **Install dependencies:**
```bash
pip install -r requirements.txt

```


3. **Data Setup:**
Ensure `train_data.zip` and `test_data.zip` are unzipped in the `data/` folder.
4. **Run Training:**
Execute the main notebook to reproduce the patch extraction and training pipeline.
