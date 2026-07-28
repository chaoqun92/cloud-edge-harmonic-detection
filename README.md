# Attention-Enhanced Autoencoder for Early Harmonic Detection in Cloud-Edge Framework

This repository provides the official implementation and synthetic/measured datasets for the paper: **"Attention-enhanced Autoencoder for early harmonic detection in cloud-edge framework"**.

---

## 📂 Repository Structure & File Descriptions

The repository consists of 5 core files that support the complete data augmentation, training, and evaluation pipeline:

### 1. Code
* **`Autoencoder_Model_Final.ipynb`**
  * Main Jupyter Notebook script for the entire experimental workflow.
  * Includes data loading, Min-Max normalization, Monte Carlo data augmentation logic, Attention-AE and Vanilla AE model construction, training, baseline (Isolation Forest) evaluation, and automatic figure/table generation (`ROC_Comparison.png`, `model_comparison_table.csv`, etc.).

### 2. Datasets
All datasets contain 8D summary feature vectors extracted at the edge layer: 
`[H3_V_avg, H5_V_avg, H7_V_avg, H3_I_avg, H5_I_avg, H7_I_avg, U_THD, I_TDD]` (along with fundamental base current `Base_I`).

* **`real_data.csv`** *(100 samples)*
  * Baseline measurement data captured via **XL803** three-phase power source and **RN8302** metering module under normal operating conditions.
  * Serves as the physical benchmark and kernel density estimation (KDE) fitting source for Monte Carlo sampling.
* **`train_data_augmented_10100.csv`** *(10,100 samples)*
  * Main training dataset comprising 100 real measured samples + 10,000 synthetic samples generated via Monte Carlo simulation with Gaussian noise ($\sigma=0.1$).
  * Pre-filtered under normal grid bounds ($U_{\text{THD}} < 3\%$ and $I_{\text{TDD}} < 4\%$).
* **`test_real_normal_new_with_base.csv`** *(200 samples)*
  * Independent normal test set generated under the same boundary conditions ($U_{\text{THD}} < 3\%$, $I_{\text{TDD}} < 4\%$) with strict zero-overlap against training data. Used to evaluate false positive rates.
* **`test_abnormal_mixed_200.csv`** *(200 samples)*
  * Anomaly test set evaluating detection sensitivity near decision boundaries:
    * **Rows 1–100**: Mild harmonic pollution ($3.2\% \le U_{\text{THD}} \le 5.5\%$, $4.2\% \le I_{\text{TDD}} \le 7.0\%$).
    * **Rows 101–200**: Boundary-fuzzy anomalies ($U_{\text{THD}} < 3\%$, $4.001\% \le I_{\text{TDD}} \le 4.5\%$).

---

## 🔄 Data Flow & Execution Pipeline

```text
[real_data.csv] (100 Real Measured Benchmark)
       │
       ▼ (Monte Carlo Augmentation & KDE Fitting)
[train_data_augmented_10100.csv] (10,100 Training Samples)
       │
       ▼
[Autoencoder_Model_Final.ipynb] (Main Script: Normalize -> Train Model)
       │
       ├────────────────────────────────────────┐
       ▼                                        ▼
[test_real_normal_new_with_base.csv]   [test_abnormal_mixed_200.csv]
(200 Normal Test Samples)              (200 Anomaly Test Samples)
       │                                        │
       └───────────────────┬────────────────────┘
                           ▼
             Combined 400 Test Samples 
     (Calculates AUC, F1-Score & ROC Curves)
