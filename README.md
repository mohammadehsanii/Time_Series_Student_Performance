# 📊 Time Series Analysis & Forecasting on Open University Learning Data

This project performs an extensive **big data pipeline and predictive time series modeling** on Open University learning data. It involves **data preprocessing with PySpark**, **visual exploration**, **feature engineering**, **stationarity testing**, and **forecasting using SARIMAX and LSTM** models. The aim is to analyze and predict student assessment scores over time based on multiple demographic and academic variables.

---

## 🔧 Setup Instructions

### 1. Install Required Libraries
```bash
pip install pyspark matplotlib scikit-learn statsmodels missingno
```

### 2. Tools Used
- `PySpark` for handling large datasets
- `Pandas/Matplotlib/Seaborn` for EDA and visualization
- `scikit-learn` for anomaly detection
- `statsmodels` for statistical tests and time series decomposition
- `TensorFlow` for deep learning-based LSTM forecasting
- `Google Colab` for GPU-accelerated model training

---

## 📂 Dataset Overview

The dataset consists of 7 CSV files:
- `courses.csv`
- `assessments.csv`
- `studentAssessment.csv`
- `studentInfo.csv`
- `studentRegistration.csv`
- `studentVle.csv`
- `vle.csv`

These files are merged using common keys to create a unified dataset for modeling and analysis.

---

## 🧹 Data Preprocessing

- **Data Merging**: Joined all datasets based on student/module identifiers.
- **Missing Values**:
  - Removed columns with excessive missing values (e.g., `week_from`, `week_to`)
  - Dropped rows with missing values in key predictors
- **Data Reduction**: Removed unnecessary columns (IDs, redundant attributes)
- **Sampling**: Due to the large size (~60M rows), sampled a small fraction for EDA

---

## 📊 Exploratory Data Analysis

- **Correlation heatmap**: Identified weak relationships among numeric variables
- **Bar plots**: Visualized demographics such as gender, age_band, region
- **Boxplots**: Explored how categorical variables influence scores
- **Scatterplots**: Investigated numeric predictors against scores

---

## 🏗️ Feature Engineering

- Encoded categorical variables based on visual/statistical insight:
  - `Gender`, `Region`, `Disability` ➝ Removed (low predictive power)
  - `Highest Education`, `Age Band`, `Final Result`, `Assessment Type` ➝ Encoded numerically
- Created binary columns (e.g., `unregistered`)
- Final dataset included both numeric and encoded categorical variables

---

## 📈 Time Series Analysis

### ✅ Goal
Forecast average assessment scores over time (`date_submitted`) by aggregating data and identifying trends.

### Key Steps:
1. **Aggregated scores** by `date_submitted` (mean and median of encoded variables)
2. **Outlier Detection** using Isolation Forest
3. **Trend and Seasonality Decomposition** via `seasonal_decompose`
4. **Stationarity Testing**:
   - ADF Test ➝ Fail to reject null ➝ Non-stationary
   - KPSS Test ➝ Reject null ➝ Non-stationary
5. **Differencing** to achieve stationarity (1st order)
6. **Stationarity Re-tested** ➝ Confirmed

---

## 🔮 Forecasting Models

### 1. SARIMAX Model (Statistical Approach)
- Includes exogenous features: `age_band_encoded`, `education_encoded`, `assessment_type_encoded`
- Order: (2,1,2), Seasonal Order: (2,1,2,30)
- **Train-Test Split**: 80/20
- **Evaluation**:
  - RMSE, MAE, MSE computed
  - Visualization of predicted vs actual values

### 2. LSTM Model (Deep Learning Approach)
- Input: Sliding windows of sequences (length = 30)
- Network:
  - 2 LSTM layers with ReLU
  - Dropout for regularization
  - Dense output layer
- **Training**:
  - 50 epochs, batch size = 32
  - Adam optimizer, MSE loss
- **Evaluation**:
  - Inverse scaling applied for meaningful predictions
  - RMSE, MAE, MSE calculated
  - Visualization of prediction accuracy

---

## 📊 Model Comparison

| Metric     | SARIMAX  | LSTM     |
|------------|----------|----------|
| RMSE       | ✓ (Good) | ✓ (Better) |
| MAE        | ✓        | ✓        |
| Accuracy   | Moderate | High     |
| Interpretability | High | Lower (Black-box) |

---
