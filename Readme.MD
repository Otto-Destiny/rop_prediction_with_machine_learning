
# Rate of Penetration (ROP) Prediction Using Machine Learning  
**A Physics-Guided, Multi-Well Learning Framework for Drilling Optimization**  

![ROP Prediction Banner](images/rop-overlay.png)

## Overview

This project presents a robust machine learning workflow for modeling and predicting the **Rate of Penetration (ROP)** during drilling operations using high-resolution real-time surface data. It combines advanced data preprocessing, physics-informed feature engineering, and nonlinear regression modeling (SVR and XGBoost) to uncover the underlying relationships between surface parameters and drilling performance.

The objective is to provide a data-driven framework that supports **ROP optimization**, **real-time advisory systems**, and **automation of performance evaluation**—critical for cost reduction and efficient drilling practices in unconventional and complex wells.

---

## Problem Statement

ROP is a complex function of mechanical energy transfer, bit-rock interaction, lithological variability, and system hydraulics. Traditional models rely on empirical fitting or basic regression which fail to generalize across sections or wells. This project addresses the question:

> *Can a feature-engineered, multi-well supervised model using real-time drilling parameters effectively predict ROP across wellbores while preserving engineering meaning and accuracy?*

---

## Dataset Origin

The dataset used consists of high-frequency surface log parameters from five field wells: **MIP3A, MIP3B, MIP5A, MIP5B, MIPSW**, which are open source datasets with diverse lithological sections. Parameters were recorded using rig surface sensors, MWD inputs, and AutoDriller setpoints.

Each .csv file corresponds to one well and contains over 100 features including:

- Depth-based mechanical variables (e.g., Hook Load, Torque, RPM)
- Time-series indicators (e.g., On-bottom hours, Circulating hours)
- Drilling control inputs (e.g., AutoDriller setpoints)
- Real-time derived values (e.g., WOB, ROP, Differential Pressure)

---

## Data Preparation & Quality Assurance

1. **Initial Audit**  
   All files are individually audited to identify nulls, static fields (e.g., `Tool Face` = constant 1234), and placeholder artifacts (-999.25). Static or irrelevant columns are pruned.

2. **Well Alignment & Timestamping**  
   Features such as `YYYY/MM/DD` and `HH:MM:SS` are merged into a unified timestamp to preserve event order across wells, enabling possible future use of temporal modeling or sequence learning.

3. **Canonical Feature Set**  
   A domain-pruned canonical schema is defined with the following retained variables:
   - `Hole Depth`, `Rate of Penetration`, `Hook Load`, `Rotary RPM`, `Rotary Torque`
   - `Weight on Bit`, `Differential Pressure`, `Standpipe Pressure`, `On Bottom Hours`

4. **Physical Filtering & Engineering Curation**  
   Outlier thresholds are determined using domain rules:
   - `RPM > 100` and `WOB > 40 Klbf` are discarded
   - `Differential Pressure` clipped to [190, 720 psi]
   - `ROP` limited to 0–385 ft/hr

   Final dataset spans **~35,000 samples** across **5 wells**, with physically consistent ranges.

---

## Feature Engineering

### Mean Specific Energy (MSE)  
Using a mechanistic formulation,
This scalar combines rotary energy and axial force to quantify energy required to remove a unit volume of rock.

### Statistical Transformations

Features with high skew (e.g., Torque, WOB) undergo:

* `log1p` transformation (for rapid correction)
* `Box-Cox` transformation (for advanced Gaussianization)

---

## Modeling Workflow

The workflow comprises the following pipeline stages:

1. **Train/Test Split**

   * Stratified split using 70/30 ratio
   * Features scaled using `MinMaxScaler` (independently applied to test and train)

2. **Model Candidates**

   * **Support Vector Regression (SVR)** with RBF kernel
     Baseline model; non-parametric, high-bias
   * **XGBoost Regressor**
     Gradient-boosted tree ensemble capturing nonlinearities and feature interactions

3. **Metrics**

   * `R² Score`: goodness of fit
   * `MAE`, `MSE`, `RMSE`: absolute and squared error magnitude
   * Feature importance using gain-based attribution from XGBoost

---

## Results Summary

| Model | Train R² | Test R² | RMSE   | MAE    |
| ----- | -------- | ------- | ------ | ------ |
| SVR   | 0.6856   | 0.6842  | 0.1093 | 0.0820 |
| XGB   | 0.9871   | 0.9807  | 0.0270 | 0.0167 |

The XGBoost model outperforms SVR significantly, capturing complex interactions between control variables (RPM, WOB) and dependent ROP outcomes.

---

## Visualization & Interpretation

* **Actual vs Predicted Scatterplots** (test set):
  Reveal tight clustering around identity line for XGB model

* **Feature Importance**:
  Ranked by information gain—Torque, RPM, and MSE dominate, confirming theoretical expectations

* **Residual Analysis**:
  No evidence of heteroskedasticity or pattern leakage

---

## Technology Stack

* **Python**: Data manipulation and ML prototyping
* **Pandas / NumPy**: Data wrangling and numerical operations
* **Seaborn / Matplotlib**: Statistical and exploratory plots
* **Scikit-learn**: Preprocessing and regression metrics
* **XGBoost**: Scalable gradient-boosted decision trees
* **SciPy**: Box-Cox transformation for distribution normalization

---

## How to Use

1. Clone this repository:

```bash
git clone https://github.com/your-username/rop-prediction
cd rop-prediction
```

2. Install requirements:

```bash
pip install -r requirements.txt
```

3. Run Jupyter notebook:

```bash
jupyter notebook ROP_Prediction.ipynb
```

4. Place well datasets in:

```
/Datasets/
├── MIP3A.csv
├── MIP3B.csv
├── MIP5A.csv
├── MIP5B.csv
├── MIPSW.csv
```

---

## Potential Extensions

* Real-time ROP prediction using rigstream data
* Model transferability across BHAs and lithofacies
* Integration with hydraulic models for rate optimization
* Deployment as a cloud microservice with API endpoints

---

## Author

**Destiny Otto**

Petroleum Engineer | Machine Learning Practitioner
with growing expertise on intelligent automation in drilling optimization and Subsurface Analytics

---

## License

This repository is provided under the MIT License. Attribution required for derivative work or publication.

