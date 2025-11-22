# Data Mining Project – MAGIC Gamma Telescope

This repository contains a data mining project for the MAGIC Gamma Telescope dataset.  
The goal is to apply three core data mining techniques on a real-world dataset:

1. **Outlier Detection**
2. **Clustering**
3. **Frequent Itemset & Association Rule Mining**

The project is implemented in **Python** using **Jupyter Notebook**.

---

## Dataset

- **Name:** MAGIC Gamma Telescope dataset  
- **Source:** UCI Machine Learning Repository  
- **File:** `magic+gamma+telescope` (or `magic04.data` in the notebook)
- **Target column:** `class`  
  - `g` → Gamma (signal)  
  - `h` → Hadron (background)

### Features

The dataset includes 10 numerical features:

- `fLength`, `fWidth`, `fSize`, `fConc`, `fConc1`
- `fAsym`, `fM3Long`, `fM3Trans`, `fAlpha`, `fDist`

---

## Project Structure

- `Data Mining Project.ipynb`  
  Initial notebook with data loading, preprocessing, outlier detection, and clustering.

- `Data_Mining_Project_UPDATE.ipynb`  
  Updated notebook including:
  - Outlier Detection
  - Clustering
  - Frequent Itemset & Association Rule Mining

- `magic+gamma+telescope`  
  Raw dataset file.

- `.gitignore`  
  Git ignore configuration.

---

## Methods

### 1. Data Preparation

- Loaded the MAGIC dataset and assigned column names.
- Checked:
  - Missing values
  - Duplicates
  - Class distribution (`g` vs `h`)
- Separated:
  - **Features**: the 10 numerical columns
  - **Target**: `class`

Features were standardized using **`StandardScaler`** before outlier detection and clustering.

---

### 2. Outlier Detection (Isolation Forest)

- Algorithm: `IsolationForest` (sklearn)
- Parameters (example):
  - `n_estimators = 100`
  - `contamination = 0.02`
  - `random_state = 42`
- Labeled records as:
  - `1` → inlier
  - `-1` → outlier
- Removed detected outliers to create a **clean dataset** (`data_clean`) used in subsequent steps.

**Goal:** remove anomalous events that may distort clustering and pattern discovery.

---

### 3. Clustering (KMeans + PCA)

- Algorithm: `KMeans`
- Features: standardized features after removing outliers.
- Tested different values of **k** (from 2 to 9) and evaluated using:
  - **Inertia**
  - **Silhouette score**
- Selected the best `k` (e.g., `k = 2`).
- Visualizations:
  - 2D **PCA projection** colored by cluster label.
  - Scatter plot of `fAlpha` vs `fDist` colored by cluster.
- Compared clusters with true `class` values using `pd.crosstab`.

**Goal:** discover natural groupings in the data and see how they relate to Gamma vs Hadron events.

---

### 4. Frequent Itemset & Association Rule Mining

Performed on the **clean dataset** (`data_clean`):

1. **Discretization**
   - Converted numerical features into 3 bins: `low`, `medium`, `high` using `pd.qcut`.
   - Mapped target:
     - `g` → `gamma`
     - `h` → `hadron`

2. **Transaction Encoding**
   - Applied `pd.get_dummies` to obtain a binary (0/1) transaction-style dataframe `transactions_df`.
   - Each column represents an **item** (e.g. `fAlpha_high`, `fDist_low`, `class_gamma`).

3. **Frequent Itemset Mining (Apriori)**
   - Library: `mlxtend.frequent_patterns.apriori`
   - Parameters:
     - `min_support = 0.05`
     - `use_colnames = True`
   - Output stored in `frequent_itemsets`.

4. **Association Rules**
   - Library: `mlxtend.frequent_patterns.association_rules`
   - Parameters:
     - `metric = "confidence"`
     - `min_threshold = 0.6`
   - Generated rules with:
     - `support`
     - `confidence`
     - `lift`
     - other metrics.

5. **Class-related Rules**
   - Filtered rules where `consequents` contain `class_gamma` or `class_hadron`.
   - Sorted by `lift` to highlight the most informative rules.
   - These rules show which feature patterns are strongly associated with Gamma vs Hadron events.

6. **Visualizations**
   - Bar chart for **Top 10 Frequent Itemsets** by support.
   - Scatter plot of **Support vs Confidence** for all association rules.

---

## How to Run

### Requirements

- Python 3.x
- Jupyter Notebook / JupyterLab
- Required libraries:
  - `pandas`
  - `numpy`
  - `matplotlib`
  - `scikit-learn`
  - `mlxtend`

Install dependencies (example):

```bash
pip install pandas numpy matplotlib scikit-learn mlxtend
