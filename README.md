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

- 🧱 **U-Net**  
- 🔁 **U-Net++**  
- 🌐 **DeepLabV3+**  
- 🛠️ **SeismoLabV3+** — Custom optimized DeepLabV3+ model, best performer

---

## 📁 Repository Structure

- **`utils.py`** — Utility functions provided by ThinkOnward for preprocessing and evaluation  
- **`SeismoLabV3+.ipynb`** — Main solution notebook implementing the optimized DeepLabV3+ segmentation network  
- **`SeismoLabV3+.pth`** — Pretrained weights for the top-performing model  
- **`README.md`** — Project documentation  
- **`LICENSE`** — License for this work  
- **`benchmarks/`** — Contains baseline and comparison models with consistent hyperparameter settings:  
  - `UNet_seismic.ipynb`  
  - `UNet++_seismic.ipynb`  
  - `DeepLabV3+_seismic.ipynb`  
  - `utils.py`

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

#### 🔹 Ground Truth (Training)
- `vp_model.npy`: Velocity model of shape `(300, 1259)`  
  Represents P-wave speed (m/s) at each subsurface location.  

> ⚠️ Test samples include only the receiver files and are used for leaderboard submission.

---

## 🧠 Geophysical Background

This dataset simulates marine seismic surveys, where an energy source emits acoustic waves into the Earth, and the reflected signals from subsurface layers are recorded by receivers. The goal is to reconstruct the **P-wave velocity model** from these seismic traces.

---

## 🛠️ Custom Model Architecture (Best Performer — SeismoLabV3+)

The top-performing model is **SeismoLabV3+**, an optimized variant of **DeepLabV3+**, enhanced through architectural tuning and encoder optimization.

### 🔧 Key Enhancements
- 🔁 **Backbone**: ResNeXt50_32x4d  
- ⛓️ **Encoder Output Stride**: 16  
- 🎛️ **Atrous Rates**: (12, 24, 36)  
- 🧠 **ASPP Dropout**: 0.5  
- 📐 **Output Resolution**: Native (300×1259), no spatial downsampling  

### ⚙️ Training Configuration
| Setting              | Value                                  |
|----------------------|----------------------------------------|
| Optimizer            | AdamW                                 |
| Learning Rate        | Tuned per backbone                     |
| Batch Size           | 4                                     |
| Epochs               | 50                                    |
| Loss Function        | Mean Absolute Percentage Error (MAPE) |
| LR Scheduler         | ReduceLROnPlateau                     |
| Validation Split     | 20% (random seed = 42)                |

---

## 🛠️ Benchmark Architectures

### 🔹 U-Net (Baseline)
Classical **encoder–decoder** with skip connections (`smp.Unet`). Strong baseline for dense regression tasks.  

**Key Settings**  
- 🔁 Backbone: ResNet50 (ImageNet-pretrained)  
- ⛓️ Encoder Depth: 5 (input padded to 320×1280)  
- 🎛️ Decoder Channels: (256, 128, 64, 32, 16)  
- 📐 Output Resolution: Cropped back to (300×1259)  

---

### 🔹 U-Net++ (Nested Skip Connections)
A nested U-Net variant (`smp.UnetPlusPlus`) with **dense skip pathways** for finer spatial detail recovery.  

**Key Settings**  
- 🔁 Backbone: ResNet50 (ImageNet-pretrained)  
- ⛓️ Encoder Depth: 5 (input padded to 320×1280)  
- 🎛️ Decoder Channels: (256, 128, 64, 32, 16)  
- 🧩 Skip Connections: Nested dense skip pathways  
- 📐 Output Resolution: Cropped back to (300×1259)  

---

### 🔹 DeepLabV3+
Standard **DeepLabV3+** segmentation network (`smp.DeepLabV3Plus`).  

**Key Settings**  
- 🔁 Backbone: ResNet50  
- ⛓️ Encoder Output Stride: 16  
- 🎛️ Atrous Rates: (12, 24, 36)  
- 🧠 ASPP Dropout: 0.5  
- 📐 Output Resolution: Native (300×1259)  

---

## 🧪 Benchmarking Results

### 📊 Evaluation Metric: Mean Absolute Percentage Error (MAPE)

In this project, model performance is evaluated using **Mean Absolute Percentage Error (MAPE)**.  
Lower values indicate higher accuracy, with **0% representing a perfect prediction**.

\[
\text{MAPE} = \frac{100\%}{N} \sum_{i=1}^{N} \left| \frac{A_i - F_i}{A_i} \right|
\]

**Why MAPE?**
- Easy to interpret: errors expressed as percentages  
- Well-suited for regression-based velocity model predictions in seismic inversion  

---

### **Table 1 – Internal 20% Test Split (60% Train / 20% Validation / 20% Test)**

| Model        | Backbone         | Pre-trained Weights | MAPE    |
|--------------|------------------|---------------------|---------|
| U-Net        | ResNet50         | ImageNet            | 0.03084 |
| U-Net++      | ResNet50         | ImageNet            | 0.03049 |
| DeepLabV3+   | ResNet50         | ImageNet            | 0.03038 |
| SeismoLabV3+ | ResNeXt50_32x4d  | ImageNet            | 0.03025 |

---

### **Table 2 – Official ThinkOnward Leaderboard Results (80% Train / 20% Validation on test set)**

| Model        | Backbone         | Pre-trained Weights | MAPE (Leaderboard) |
|--------------|------------------|---------------------|--------------------|
| U-Net        | ResNet50         | ImageNet            | 0.033172           |
| U-Net++      | ResNet50         | ImageNet            | 0.032766           |
| DeepLabV3+   | ResNet50         | ImageNet            | 0.031762           |
| SeismoLabV3+ | ResNeXt50_32x4d  | ImageNet            | 0.031246           |

---

## 💾 Model Checkpoints

Pretrained weights for the top-performing model are provided in the repository root:

- 📁 [`SeismoLabV3+.pth`](SeismoLabV3+.pth)

---

## 📎 License & Attribution

This dataset and benchmark are part of the [Speed & Structure Challenge 2025](https://thinkonward.com/app/c/challenges/speed-and-structure/data) by [ThinkOnward](https://thinkonward.com).  

Licensed under the **[Creative Commons Attribution 4.0 License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/)**.

---

## 📬 Contact

**Mahedi Hasan**  
📧 Email: [mahedishuvro2030@gmail.com](mailto:mahedishuvro2030@gmail.com)  
📘 LinkedIn: [linkedin.com/in/mahedi-hasan-b63394243](https://www.linkedin.com/in/mahedi-hasan-b63394243)  
📍 Launceston, Tasmania, Australia

---

## 📚 References

- Arize AI. *Mean Absolute Percentage Error (MAPE): What You Need to Know*. 2023. [Read here](https://arize.com/blog-course/mean-absolute-percentage-error-mape-what-you-need-to-know/)  
- Iakubovskii, Pavel. *Segmentation Models Pytorch*. GitHub repository, 2019. [https://github.com/qubvel/segmentation_models.pytorch](https://github.com/qubvel/segmentation_models.pytorch)  





