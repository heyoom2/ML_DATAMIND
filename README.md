# ML_DATAMIND
2025-2 ML teamproject

---

# 🌐 Tor Website Fingerprinting Project

This project explores **website fingerprinting attacks on the Tor network**, analyzing encrypted traffic patterns to classify which website a user is visiting — even when traffic contents are hidden.
We implement and evaluate models under **Closed-world** and **Open-world** settings using various extracted traffic features.

---

## 📁 Repository Structure

```
📦 ML_DATAMIND
├── Closed_World/
│   └── closed_scenario.ipynb                   # Random Forest training & evaluation (Closed-world)
│
├── Data_Preprocessing/
│   ├── Burst_Feature_Extraction.ipynb
│   ├── Burst_Feature_Extraction_final.ipynb    # final version
│   ├── Cumulative_Feature_Extraction.ipynb
│   ├── InOut_Feature_Extraction.ipynb
│   ├── InOut_Feature_Extraction_final.ipynb    # final version
│   └── Features_Concat.ipynb                   # ⚠️ Run this last to merge all features
│
├── Dataset/
│   ├── features_burst/inout/cum_mon/unmon.npy  # Feature Extraction Result
│   ├── X/y_train/test_cw.npy                   # Closed-world preprocessed data
│   ├── X/y_train/test_bin.npy                  # Open-world binary data
│   └── X/y_train/test_mc.npy                   # Open-world multi-class data
│
├── Open_Binary/                                # Tuned versions for comparison
│
├── Open_Multi-Class/
│   ├── multiclass_train.ipynb
│   └── multiclass_evaluation.ipynb
│
└── README.md
```

---

## How to Run the Project

### Step 1. Mount Google Drive (Colab)

Before running any notebook, mount your Google Drive to access datasets:

```python
from google.colab import drive
drive.mount('/content/drive')
```

Make sure your dataset folder (e.g., `ML_Dataset/`) is located in your Google Drive.
All `.pkl` and `.npy` files should be accessible via paths like:

```
/content/drive/MyDrive/ML_Dataset/
```

---

### Step 2. Feature Extraction

Run the preprocessing notebooks in the following order (inside `/Data_Preprocessing/`):

1. `Burst_Feature_Extraction_final.ipynb`
2. `InOut_Feature_Extraction_final.ipynb`
3. `Cumulative_Feature_Extraction.ipynb`
4. `Features_Concat.ipynb` → merges all features and saves them as `.npy` files

Output files (saved to `/Dataset/`):

* `features_burst_mon.npy`, `features_inout_mon.npy`, `features_cum_mon.npy`
* `features_burst_unmon.npy`, `features_inout_unmon.npy`, `features_cum_unmon.npy`
* Scenario data: `X/y_train/test_cw.npy`, `X/y_train/test_bin.npy`, `X/y_train/test_mc.npy`

---

### Step 3. Model Training

#### 🧠 Closed-world

Open and run `/Closed_World/closed_scenario.ipynb`

* Loads: `X/y_train/test_cw.npy`
* Model: `RandomForestClassifier(n_estimators=200)`
* Outputs: Accuracy, confusion matrix, and feature importance

---

#### 🌍 Open-world Binary

Open and run `/Open_Binary/*.ipynb`

* Loads: `X/y_train/test_bin.npy`
* Model configuration:

  ```python
  n_estimators=300,
  criterion='entropy',
  class_weight='balanced',
  max_depth=20,
  min_samples_split=10,
  min_samples_leaf=5
  ```
* Outputs: Accuracy, ROC-AUC, Confusion Matrix, Feature Importance

---

#### 🏷️ Open-world Multi-class

Open and run `/Open_Multi-Class/multiclass_train.ipynb & evaluate.ipynb`

* Loads: `X/y_train/test_mc.npy`
* Model: `RandomForestClassifier(n_estimators=200, criterion='entropy')`
* Evaluates 95 monitored sites and additional unmonitored class (-1)

---

## ⚙️ Project Overview

* **Goal:** Implement a traffic analysis model that classifies website visits using Tor traffic traces.
* **Scenarios:**

  1. **Closed-world:** Only monitored (known) websites are used for training and testing.
  2. **Open-world (Binary):** Classify between monitored vs. unmonitored websites.
  3. **Open-world (Multi-class):** Identify 95 monitored websites and classify others as “unmonitored.”

---

## 🧩 Features

| Feature Type    | Description                                                                        |
| --------------- | ---------------------------------------------------------------------------------- |
| **Burst**       | Captures short-term packet bursts representing request–response activity patterns. |
| **In/Out Flow** | Quantifies directionality (incoming vs. outgoing packet ratios).                   |
| **Cumulative**  | Summarizes long-term trends in cumulative packet size changes.                     |

Each feature was extracted and saved separately for both **monitored (mon)** and **unmonitored (unmon)** datasets, then merged for final training.

---

## 🧮 Model & Hyperparameters

We adopted **Random Forest Classifier** for all experiments due to its robustness to noise, interpretability, and suitability for nonlinear traffic patterns.

| Scenario             | Key Hyperparameters                                                                                                                | Notes                                                        |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| **Closed-world**     | `n_estimators=200`                                                                                                                 | Focus on basic learning performance                    |
| **Open Binary**      | `n_estimators=300`, `criterion='entropy'`, `class_weight='balanced'`, `max_depth=20`, `min_samples_split=10`, `min_samples_leaf=5` | Addressed class imbalance between monitored/unmonitored data |
| **Open Multi-class** | `n_estimators=200`, `criterion='entropy'`                                                                                          | Optimized for 95-class fine-grained classification           |

---

## 🧠 Experimental Environment

* **Platform:** Google Colab
* **Hardware:** CPU, about 12 GB RAM
* **Storage:** Google Drive integration for dataset management
* **Dataset size:** ~19,000 monitored + 3,000 unmonitored traces
* **Data Split:** 75% train / 25% test
* **Feature Dimension:** 25 combined numerical features

---
