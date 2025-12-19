# Data Mining Project – MAGIC Gamma Telescope

This repository contains a data mining project for the **MAGIC Gamma Telescope dataset**.

The goal is to apply three core techniques on a real-world dataset and produce clear, reproducible artifacts:
* **Outlier Detection**
* **Clustering**
* **Frequent Itemset & Association Rule Mining**

The project is implemented in Python using Jupyter Notebook. Running the notebook will save all figures to `reports/figures/` for grading and review.

---

## Dataset

* **Name:** MAGIC Gamma Telescope dataset
* **Source:** [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/datasets/magic+gamma+telescope)
* **File:** `magic+gamma+telescope/magic04.data`
* **Target column:** `class`
    * `g` → Gamma (signal)
    * `h` → Hadron (background)

### Features
The dataset includes **10 numerical features**:
> `fLength`, `fWidth`, `fSize`, `fConc`, `fConc1`, `fAsym`, `fM3Long`, `fM3Trans`, `fAlpha`, `fDist`


## Project Structure

```text            
├── Data Mining Project.ipynb            # Final end-to-end notebook
├── magic+gamma+telescope/               # Raw dataset directory
├── reports/figures/                     # Generated figures (Created at runtime)
└── .gitignore                           # Git ignore configuration
```
---
## Methods

### 1) Data Preparation
* **Load:** Import MAGIC dataset and assign column names.
* **Quality Checks:** Check for missing values, duplicates, and class balance.
* **Split:**
  * **Features:** The 10 numerical columns.
  * **Target:** `class`.
* **Scaling:** Standardize features with `StandardScaler` (required for Isolation Forest and K-Means).

### 2) Outlier Detection — Isolation Forest
* **Algorithm:** `IsolationForest` (scikit-learn)
* **Parameters:** `n_estimators=100`, `contamination=0.02`, `random_state=42`.
* **Output:** `1` (inlier), `-1` (outlier).
* **Action:** Remove outliers to create a `data_clean` dataset for subsequent steps.
* **Visualizations:**
  * `anomaly_score_hist.png`: Histogram of anomaly scores.
  * `anomalies_pca2d.png`: 2D PCA projection highlighting outliers.

### 3) Clustering — K-Means (+ PCA)
* **Algorithm:** `KMeans` on standardized features (post-outlier removal).
* **Model Selection:** Evaluate $K \in \{2, \dots, 9\}$ using **Elbow (Inertia)** and **Silhouette Score**.
* **Comparison:** Cross-tabulate clusters against original classes (Gamma/Hadron).
* **Visualizations:**
  * `kmeans_elbow.png`: Inertia across K.
  * `kmeans_silhouette.png`: Silhouette score across K.
  * `clusters_pca2d.png`: 2D PCA projection colored by cluster.
  * `clusters_fAlpha_fDist.png`: Scatter in original feature space (`fAlpha` vs `fDist`).

### 4) Frequent Itemset & Association Rule Mining
Performed on `data_clean` after discretization.

* **Discretization:** Bin numeric features into low/medium/high using `pd.qcut` (3 quantiles). Map target to `class_gamma`/`class_hadron`.
* **Encoding:** Use `pd.get_dummies` to create a binary transaction matrix.
* **Frequent Itemsets:** `apriori` (`min_support=0.05`).
* **Association Rules:** `association_rules` (`metric="confidence"`, `min_threshold=0.6`).
* **Filtering:** Focus on rules where the consequent implies a specific class (`class_gamma` or `class_hadron`), sorted by Lift.
* **Visualizations:**
  * `top10_itemsets.png`: Bar chart of frequent itemsets.
  * `association_network.png`: Network graph (nodes=items, edges=rules).
  * `rules_support_vs_confidence.png`: Scatter plot.
  * `rules_lift_hist.png` / `rules_confidence_hist.png`: Metric distributions.


## How to Run

### Requirements
* Python 3.10+ (recommended)
* Jupyter Notebook / JupyterLab

### Installation
Install the core dependencies:
```bash
pip install pandas numpy matplotlib scikit-learn mlxtend networkx
```

### Execution
1. Launch Jupyter.
2. Open `Data_Mining_Project_UPDATE.ipynb`.
3. Run all cells top-to-bottom.
4. Check `reports/figures/` for the generated outputs.

---

## Reproducibility
* **Fixed Seeds:** `random_state=42` used for all stochastic algorithms (IsolationForest, KMeans).
* **Pipeline:** Standardized preprocessing applied consistently.
* **Artifacts:** All figures saved with deterministic filenames.

---

## Results Summary
* **Outliers:** Isolation Forest flags the most anomalous ~2% of observations.
* **Clustering:** Optimal $K$ chosen via elbow/silhouette analysis; visual separation confirmed via PCA.
* **Rules:** Strong associations found between discretized features and particle class, visualized via network graphs and metric plots.

## Visualization Index

| Figure Filename | Description |
| :--- | :--- |
| `anomaly_score_hist.png` | Isolation Forest anomaly score distribution. |
| `anomalies_pca2d.png` | PCA view highlighting outliers vs inliers. |
| `kmeans_elbow.png` | Inertia vs K for elbow selection. |
| `kmeans_silhouette.png` | Silhouette score vs K. |
| `clusters_pca2d.png` | 2D PCA projection of clusters. |
| `clusters_fAlpha_fDist.png` | Clusters in original feature space (fAlpha vs fDist). |
| `top10_itemsets.png` | Top-10 frequent itemsets by support. |
| `rules_support_vs_confidence.png` | Scatter plot: Support vs Confidence. |
| `rules_confidence_hist.png` | Distribution of rule confidence. |
| `rules_lift_hist.png` | Distribution of rule lift. |
| `association_network.png` | Network graph (edges=rules, width ~ confidence). |

---

## Limitations & Next Steps
* **Discretization:** Fixed quantiles may hide fine-grained numeric structure; could explore alternative binning or FP-Growth.
* **Clustering:** Consider density-based methods (DBSCAN/HDBSCAN) or manifold projections (UMAP) for non-linear structures.
* **Evaluation:** Compare outlier methods (LOF, One-Class SVM) and evaluate rule interestingness beyond Lift (e.g., Leverage, Conviction).

---

## Citations
* **Dataset:** MAGIC Gamma Telescope — UCI Machine Learning Repository.
* **Libraries:** `scikit-learn`, `mlxtend`, `matplotlib`, `networkx`, `pandas`, `numpy`.

