# Network Intrusion Detection System with Explainable AI (XAI)

A machine learning pipeline for detecting and classifying network intrusions using the **CICIDS2017** dataset. The project covers binary and multi-class classification, deep learning-based data augmentation with an Autoencoder + WGAN, and model explainability via SHAP and LIME.

---

## Table of Contents

- [Overview](#overview)
- [Dataset](#dataset)
- [Project Pipeline](#project-pipeline)
- [Models](#models)
- [Explainability (XAI)](#explainability-xai)
- [Requirements](#requirements)
- [Usage](#usage)
- [Results](#results)
- [Project Structure](#project-structure)

---

## Overview

This project builds a Network Intrusion Detection System (NIDS) that:

- Classifies network traffic as **benign or malicious** (binary) and identifies the **specific attack type** (multi-class, 7 classes).
- Addresses class imbalance using a **deep learning Autoencoder + Wasserstein GAN (WGAN)** for synthetic minority sample generation.
- Provides **model interpretability** using SHAP (global) and LIME (per-class local explanations).

---

## Dataset

**CICIDS2017** — Canadian Institute for Cybersecurity Intrusion Detection Evaluation Dataset 2017.

| File | Description |
|------|-------------|
| `Monday-WorkingHours.pcap.csv` | Benign traffic only |
| `Tuesday-WorkingHours.pcap.csv` | FTP-Patator, SSH-Patator |
| `Wednesday-WorkingHours.pcap.csv` | DoS Hulk, DoS GoldenEye, DoS slowloris, DoS Slowhttptest, Heartbleed |
| `Thursday-WorkingHours-Morning-WebAttacks.pcap.csv` | Web Attacks (Brute Force, XSS, SQL Injection) |
| `Thursday-WorkingHours-Afternoon-Infilteration.pcap.csv` | Infiltration |
| `Friday-WorkingHours-Morning.pcap.csv` | Botnet ARES |
| `Friday-WorkingHours-Afternoon-PortScan.pcap.csv` | PortScan |
| `Friday-WorkingHours-Afternoon-DDos.pcap.csv` | DDoS |

**Total records loaded:** ~2.83 million rows across 8 CSV files, each with **79 features**.

**7 consolidated attack classes used:**

| Class | Training Samples |
|-------|-----------------|
| BENIGN | 439,962 |
| DoS | 40,782 |
| DDoS | 26,682 |
| PortScan | 19,138 |
| Infiltration | 1,940 |
| Web Attack | 466 |
| Bot | 397 |

> If dataset files are not found locally, the notebook falls back to auto-generated synthetic data for testing.

---

## Project Pipeline

```
Raw CSVs (8 files)
       │
       ▼
 1. Data Loading & Merging
       │
       ▼
 2. Cleaning (drop NaN, Inf, duplicates, strip column names)
       │
       ▼
 3. Label Consolidation → 7 attack classes
       │
       ▼
 4. EDA (class distribution, feature correlation)
       │
       ▼
 5. Preprocessing
    ├── OrdinalEncoder (categorical features)
    ├── MinMaxScaler (numerical features)
    └── SelectKBest / f_classif (feature selection)
       │
       ▼
 6. Train/Test Split (stratified, 75/25)
       │
       ▼
 7. Baseline ML Models (Binary + Multi-class)
    ├── Random Forest
    ├── Gradient Boosting
    ├── Decision Tree
    └── KNN
       │
       ▼
 8. Deep Learning Augmentation
    ├── Autoencoder (50 epochs, batch=256, EarlyStopping)
    └── WGAN (100 epochs, latent space augmentation)
       │
       ▼
 9. Augmented ML Models (Binary + Multi-class)
       │
       ▼
10. Evaluation
    ├── Accuracy, Precision, Recall, F1-Score
    ├── Confusion Matrix
    └── ROC-AUC Curve
       │
       ▼
11. Explainability
    ├── SHAP (global feature importance)
    └── LIME (per-class local explanations)
```

---

## Models

### Classical ML Classifiers

| Model | Task |
|-------|------|
| Random Forest | Binary + Multi-class |
| Gradient Boosting | Binary + Multi-class |
| Decision Tree | Binary + Multi-class |
| K-Nearest Neighbors (KNN) | Binary + Multi-class |

### Deep Learning

| Component | Architecture | Purpose |
|-----------|-------------|---------|
| **Autoencoder** | Dense encoder-decoder, Adam optimizer, MSE loss, 50 epochs | Feature compression + reconstruction for WGAN input |
| **WGAN (Wasserstein GAN)** | Generator + Critic (Dense layers), 100 epochs | Synthetic minority class sample generation to address imbalance |

The AE+WGAN pipeline encodes attack-class samples into a latent representation, trains the WGAN in that space, and decodes generated samples back to feature space for augmentation.

---

## Explainability (XAI)

### SHAP (Global)
- Computes **mean absolute SHAP values** across the test set using the Random Forest model.
- Produces a ranked bar chart of the top features contributing to predictions globally.

### LIME (Per-Class Local)
- Generates **top-10 feature importance** explanations per attack class.
- Uses `LimeTabularExplainer` with the Random Forest as the black-box model.
- Visualised as horizontal bar charts (9-panel grid) with Mean LIME weights per class.

**Attack classes explained:** BENIGN, Bot, DDoS, DoS, Infiltration, PortScan, Web Attack.

Key features identified across classes include `Fwd Packet Length Std`, `Flow_IAT_Regularity`, `Destination Port`, `Subflow Fwd Bytes`, `Init_Win_bytes_backward`, and `Bwd IAT Std`.

---

## Requirements

```
Python >= 3.10
tensorflow >= 2.20
scikit-learn
numpy
pandas
matplotlib
seaborn
shap
lime
```

Install all dependencies:

```bash
pip install numpy pandas matplotlib seaborn shap lime tensorflow scikit-learn
```

---

## Usage

1. **Clone the repository** and place the CICIDS2017 CSV files in the same directory as the notebook.

2. **Launch Jupyter:**
   ```bash
   jupyter notebook Intrusion_Detection_System.ipynb
   ```

3. **Run all cells** in order. The notebook is self-contained — if CSV files are missing, it generates sample data automatically and continues.

4. **Expected outputs per section:**
   - Class distribution plots
   - Correlation heatmaps
   - Autoencoder training loss curve
   - WGAN training loss curve (Generator + Critic)
   - Model comparison bar charts (before/after augmentation)
   - Confusion matrices and ROC-AUC curves
   - SHAP summary plot
   - LIME per-class feature importance charts

---

## Results

### Binary Classification (Before vs After AE/WGAN Augmentation)

The notebook prints a side-by-side comparison table for all 4 models showing changes in Accuracy, Precision, Recall, and F1-Score after augmentation.

### Multi-Class Classification

A full **7-class × 4-model** metric table (Precision, Recall, F1-Score, Support) is generated for both pre- and post-augmentation runs.

> Note: Exact metric values depend on the dataset split seed and augmentation run. The notebook prints all results inline.

---

## Project Structure

```
.
├── Intrusion_Detection_System.ipynb   # Main notebook
├── Monday-WorkingHours.pcap.csv
├── Tuesday-WorkingHours.pcap.csv
├── Wednesday-WorkingHours.pcap.csv
├── Thursday-WorkingHours-Morning-WebAttacks.pcap.csv
├── Thursday-WorkingHours-Afternoon-Infilteration.pcap.csv
├── Friday-WorkingHours-Morning.pcap.csv
├── Friday-WorkingHours-Afternoon-PortScan.pcap.csv
├── Friday-WorkingHours-Afternoon-DDos.pcap.csv
└── README.md
```

---

## References

- Sharafaldin, I., Lashkari, A. H., & Ghorbani, A. A. (2018). *Toward Generating a New Intrusion Detection Dataset and Intrusion Traffic Characterization.* ICISSP 2018. — [CICIDS2017 Dataset](https://www.unb.ca/cic/datasets/ids-2017.html)
- Lundberg, S. M., & Lee, S.-I. (2017). *A Unified Approach to Interpreting Model Predictions.* NeurIPS 2017. — SHAP
- Ribeiro, M. T., Singh, S., & Guestrin, C. (2016). *"Why Should I Trust You?": Explaining the Predictions of Any Classifier.* KDD 2016. — LIME
- Arjovsky, M., Chintala, S., & Bottou, L. (2017). *Wasserstein GAN.* arXiv:1701.07875.
