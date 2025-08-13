# 🌍 Benchmarking Deep Segmentation Networks for Seismic Velocity Inversion

**📄 Paper Title**  
*Seismic Velocity Inversion from Shot Gathers Using Deep Segmentation Networks: Benchmarking U-Net Variants and an Optimized DeepLabV3+ Model*

**🎯 Goal**  
Develop and benchmark deep learning models to reconstruct subsurface velocity maps from synthetic seismic shot gathers.

**📌 Target Journal**  
IEEE Transactions on Geoscience and Remote Sensing (TGRS)

---

## 📦 Overview

This repository benchmarks several deep segmentation architectures for seismic velocity inversion using synthetic shot gather data. The following models are evaluated:

- 🧱 U-Net  
- 🔁 U-Net++  
- 🔍 SCU-Net++  
- 🌐 DeepLabV3  
- 🛠️ Custom optimized DeepLabV3+ model (best performer)

The final model is submitted to the **Speed & Structure Challenge**, hosted by [ThinkOnward](https://thinkonward.com).

---

## 📁 Repository Structure

seismic-velocity-inversion/
├── models/         # U-Net, U-Net++, SCU-Net++, DeepLabV3+, and custom models
├── datasets/       # PyTorch Dataset class and data loading utilities
├── scripts/        # Training, evaluation, and submission pipeline
├── utils/          # MAPE calculation, visualization, and submission helpers
├── results/        # Prediction outputs, visualizations, logs
├── checkpoints/    # Saved model weights (best performers)
├── requirements.txt # Python package dependencies
└── README.md       # Project documentation


---

## 📊 Dataset

We use a **synthetic dataset** of shot gather recordings and paired ground truth velocity models provided by the [Speed & Structure Challenge](https://thinkonward.com/app/c/challenges/speed-and-structure/data).

### 📁 Dataset Structure

- **Training Set**: 2,000 samples  
- **Test Set**: 150 samples  
- Each sample is stored in a folder named after the sample ID (e.g., `sample_0001/`)

#### 🔹 Input (for each sample)
Five synthetic seismic shot gather files:

- `receiver_data_src_1.npy`  
- `receiver_data_src_75.npy`  
- `receiver_data_src_150.npy`  
- `receiver_data_src_225.npy`  
- `receiver_data_src_300.npy`

Shape: `(300, 1259)`  
- 300 = time steps  
- 1259 = receiver channels

#### 🔹 Ground Truth (Training Only)

- `vp_model.npy`: Velocity model of shape `(300, 1259)`  
  Represents P-wave speed (in m/s) at each subsurface location.

> ⚠️ Test samples include only the receiver files and are used for leaderboard submission.

---

## 🧠 Geophysical Background

This dataset simulates marine seismic surveys, where an energy source emits acoustic waves into the Earth, and the reflected signals from subsurface layers are recorded by receivers. The goal is to reconstruct the **P-wave velocity model** from these seismic traces.

---

## 🛠️ Custom Model Architecture (Best Performer)

Our top-performing model is an optimized variant of **DeepLabV3+**, enhanced through architectural tuning and encoder optimization.

### 🔧 Key Enhancements

- 🔁 **Backbone**: ResNet50  
- ⛓️ **Encoder Output Stride**: 16  
- 🎛️ **Atrous Rates**: (12, 24, 36)  
- 🧠 **ASPP Dropout**: 0.5  
- 📐 **Output Resolution**: Native (300×1259), no spatial downsampling

### ⚙️ Training Configuration

| Setting              | Value                     |
|----------------------|---------------------------|
| Optimizer            | AdamW                     |
| Learning Rate        | Tuned per backbone        |
| Batch Size           | 4                         |
| Epochs               | 50                        |
| Loss Function        | Mean Absolute Percentage Error (MAPE) |
| LR Scheduler         | ReduceLROnPlateau         |
| Validation Split     | 20% (random seed = 42)    |
   |

---


## 🧪 Benchmarking Results
### 📊 Evaluation Metric: Mean Absolute Percentage Error (MAPE)

In this project, model performance is evaluated using the **Mean Absolute Percentage Error (MAPE)**.  
MAPE measures the average magnitude of errors between predicted and actual values, expressed as a percentage.  
Lower values indicate higher accuracy, with **0% representing a perfect prediction**.

**Formula:**

\[
\text{MAPE} = \frac{100\%}{N} \sum_{i=1}^{N} \left| \frac{A_i - F_i}{A_i} \right|
\]

Where:
- \( N \) — number of data points  
- \( A_i \) — actual (ground truth) value  
- \( F_i \) — forecasted (predicted) value  

**Why MAPE?**
- **Interpretability**: Errors are expressed as percentages, making them easy to understand.  
- **Suitability**: Ideal for regression-based velocity model predictions in seismic inversion.
- 
All models were trained on the **2,000-sample training set** and evaluated on:  
1. **Internal Split:** 60% train / 20% validation / 20% test  
2. **Official Leaderboard:** 150 hidden test samples, scored by **Mean Absolute Percentage Error (MAPE)** 

### **Table 1 – Internal 20% Test Split (60% Train / 20% Validation / 20% Test)**

| Model        | Backbone         | Pre-trained Weights | MAPE    |
|--------------|------------------|---------------------|---------|
| U-Net        | ResNet50         | ImageNet            |  |
| U-Net++      | ResNet50         | ImageNet            |  |
| DeepLabV3+   | ResNet50         | ImageNet            |  |
| DeepLabV3+   | ResNeXt50_32x4d  | ImageNet            |  |
| DeepLabV3+   | ResNeXt50_32x4d  | SWSL                |  |
| DeepLabV3+   | ResNeXt50_32x4d  | SSL                 |  |

---

### **Table 2 – Official ThinkOnward Leaderboard Results**

| Model        | Backbone         | Pre-trained Weights | MAPE (Leaderboard) |
|--------------|------------------|---------------------|--------------------|
| U-Net        | ResNet50         | ImageNet            | --                 |
| U-Net++      | ResNet50         | ImageNet            | --                 |
| DeepLabV3+   | ResNet50         | ImageNet            | --                 |
| DeepLabV3+   | ResNeXt50_32x4d  | ImageNet            | --                 |
| DeepLabV3+   | ResNeXt50_32x4d  | SWSL                | --                 |
| DeepLabV3+   | ResNeXt50_32x4d  | SSL                 | --                 |

## 💾 Model Checkpoints

Pretrained weights for the top-performing model:

- 📁 [`custom_model_best.pth`](models/checkpoints/custom_model_best.pth) *(update name if needed)*

---

## 📎 License & Attribution

This dataset and benchmark are part of the [Speed & Structure Challenge 2025](https://thinkonward.com/app/c/challenges/speed-and-structure/data) by [ThinkOnward](https://thinkonward.com).

Licensed under the **[Creative Commons Attribution 4.0 License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/)**.

---

## 📬 Contact

## 📬 Contact

**Mahedi Hasan**  
📧 Email: [mahedishuvro2030@gmail.com](mailto:mahedishuvro2030@gmail.com)  
📘 LinkedIn: [linkedin.com/in/mahedi-hasan-b63394243](https://www.linkedin.com/in/mahedi-hasan-b63394243)  
📍 Location: Launceston, Tasmania, Australia

---
**Reference:**  
Arize AI. *Mean Absolute Percentage Error (MAPE): What You Need to Know*. 2023.  
Available at: [https://arize.com/blog-course/mean-absolute-percentage-error-mape-what-you-need-to-know/](https://arize.com/blog-course/mean-absolute-percentage-error-mape-what-you-need-to-know/)  
Accessed: 13 Aug 2025.


