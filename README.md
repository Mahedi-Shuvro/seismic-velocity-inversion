# 🌍 Benchmarking Deep Segmentation Networks for Seismic Velocity Inversion

**📄 Paper Title**  
*Benchmarking Deep Segmentation Networks for Seismic Velocity Inversion on Synthetic Shot Gather Data: From U-Net Variants to a Custom Enhanced Model*

**🎯 Goal**: Develop and benchmark deep learning models to reconstruct subsurface velocity maps from synthetic seismic shot gathers.  
**📌 Target Journal**: IEEE Transactions on Geoscience and Remote Sensing (TGRS)

---

## 📦 Overview

This repository benchmarks multiple deep segmentation models for seismic velocity inversion using synthetic shot gather data.  
We experiment with and compare the following architectures:

- 🧱 U-Net
- 🔁 U-Net++
- 🔍 SCU-Net++
- 🌐 DeepLabV3+
- 🛠️ A custom enhanced model (based on SCU-Net++)

The best model is selected and submitted to the **Speed & Structure Challenge**, hosted by ThinkOnward.

---

## 📁 Repository Structure

seismic-velocity-inversion/
├── models/ # Model architectures (U-Net, U-Net++, SCU-Net++, etc.)
├── datasets/ # PyTorch Dataset class for training/test data
├── scripts/ # Training, evaluation, and submission scripts
├── utils/ # Metrics, visualization, submission helpers
├── results/ # Visualizations, saved predictions, plots
├── requirements.txt # Required packages
└── README.md # Project documentation


---

## 📊 Dataset

This project uses a **synthetic dataset** of seismic shot gather records and paired ground truth velocity models, provided as part of the **Speed & Structure Challenge** by [ThinkOnward](https://thinkonward.com/app/c/challenges/speed-and-structure/data).

### 📁 Dataset Structure

- **Training Set**: 2,000 samples  
- **Test Set**: 150 samples  
- Each sample is stored in a folder named after the sample ID (e.g., `sample_0001/`).

#### 🔹 Input Features (per sample)
Each sample contains 5 synthetic seismic shot gather inputs:
receiver_data_src_1.npy
receiver_data_src_75.npy
receiver_data_src_150.npy
receiver_data_src_225.npy
receiver_data_src_300.npy
- Shape: `(300, 1259)`  
  - 300: Time steps  
  - 1259: Receiver channels  
  - These files simulate echo returns for different seismic source positions

#### 🔹 Ground Truth (Training Only)
- `vp_model.npy`: A velocity model of shape `(300, 1259)`  
  Represents the true P-wave propagation speed (in m/s) at each subsurface location.

> 📌 Test samples include only the receiver files (no `vp_model.npy`) and are used for leaderboard submission.

---

### 🧠 Geophysical Background

This dataset simulates marine seismic surveys. An energy source (e.g., air gun) emits waves into the Earth, and returning reflections from geological boundaries are captured by receivers. The task is to reconstruct the subsurface **velocity model** from these seismic traces.

---
### 📎 License & Attribution

This dataset is provided as part of the **[Speed & Structure Challenge 2025](https://thinkonward.com/app/c/challenges/speed-and-structure/data)** hosted by [ThinkOnward](https://thinkonward.com).  
It is distributed under the **[Creative Commons Attribution 4.0 License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/)**:

## 🛠️ Custom Model Architecture (Best Performer)

The best result was achieved with a custom model based on **- U-Net/U-Net++/SCU-Net++/DeepLabV3+**, enhanced as follows:

- 🔁 Backbone: 
- 🔄 Skip Connections: 
- 🔍 Attention: 
- 🧱 Blocks: 
- 📐 Output Resolution: Native (300 × 1259), no downsampling

### ⚙️ Training Settings for Custom Model

- Optimizer: 
- Learning Rate:  
- Scheduler: 
- Epochs: 
- Batch Size:  
- Loss Function: Mean Absolute Percentage Error (MAPE)  
- Validation Split: 20% (random seed = 42)  
- Early stopping: Enabled (patience = )
This model achieved the lowest leaderboard MAPE:


## 🧪 Benchmarking Results

All models were trained on the same 2,000-sample training set and evaluated on the **150-sample test set** using the official **Speed & Structure Challenge leaderboard**, which computes the average **Mean Absolute Percentage Error (MAPE)** across all samples.

The `.npz` submission format and scoring logic follow the official evaluation protocol.

| Model          | Leaderboard MAPE ↓ | Notes                     |
|----------------|---------------------|---------------------------|
| U-Net          |                     | Baseline segmentation     |
| U-Net++        |                     | Nested skip connections   |
| DeepLabV3+     |                     | Multi-scale context       |
| SCU-Net++      |                     | Lightweight + accurate    |
| 🔥 Custom Model | **0.0693**         | Best performer            |

> 📌 MAPE = Mean Absolute Percentage Error (lower is better)
>  
> 📤 All scores are based on official ThinkOnward leaderboard submissions.
