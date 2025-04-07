# 📊 **Time Series Analysis of Student Performance at Open University**

---

## 🔍 1. **Project Overview**

This project analyzes and forecasts student performance using the **Open University Learning Analytics Dataset (OULAD)**. The goal is to understand temporal patterns in student scores, detect anomalies, and build models that predict performance over time. We leveraged **PySpark** for large-scale data processing and implemented both **statistical (SARIMAX)** and **deep learning (LSTM)** models for forecasting.

---

## 🧰 2. **Technologies Used**

- **Big Data Processing**: PySpark  
- **Data Analysis & Visualization**: Pandas, Matplotlib, Seaborn  
- **Time Series Modeling**: Statsmodels (ADF, KPSS, SARIMAX)  
- **Machine Learning**: Scikit-learn (Isolation Forest)  
- **Deep Learning**: TensorFlow/Keras (LSTM)  

---

## 📂 3. **Dataset Description**

The dataset includes 7 files from Open University’s records of over **60 million rows**, covering:

| File                  | Description                                      |
|-----------------------|--------------------------------------------------|
| `studentInfo.csv`     | Demographic and final result data                |
| `studentAssessment.csv` | Assessment submission and score info            |
| `studentVle.csv`      | Clickstream activity by students                 |
| `vle.csv`             | Metadata of learning activities                  |
| `assessments.csv`     | Assessment types, weights, and dates             |
| `studentRegistration.csv` | Registration/unregistration info             |
| `courses.csv`         | Course structure and presentation dates          |

---

## 🧹 4. **Data Processing Workflow**

### 4.1 Merging
All files were merged using PySpark via shared keys such as `id_student`, `id_assessment`, `code_module`, and `code_presentation`.

### 4.2 Missing Data Handling
- Columns with excessive missing data (`week_from`, `week_to`) were dropped.
- Rows with missing `score`, `imd_band`, `date_registration`, or `date` were removed.

### 4.3 Dimensionality Reduction
Features with no clear impact on score were dropped:
- **Removed**: gender, region, disability, imd_band, course identifiers, etc.
- **Encoded**:
  - `age_band`: encoded as `1` if `55<=`, else `0`
  - `highest_education`: encoded as `1` if `Post Graduate Qualification`, else `0`
  - `final_result`: 0 = Fail/Withdrawn, 1 = Pass, 2 = Distinction
  - `assessment_type`: 0 = Exam, 1 = TMA, 2 = CMA

---

## 📊 5. **Exploratory Data Analysis (EDA)**

- **Correlation Heatmap**: No strong linear correlation found between numeric variables and score.
- **Boxplots**: Showed minimal differences across gender, region, disability, suggesting little predictive power.
- **Education & Assessment Type**: Found to impact scores significantly.

---

## 📈 6. **Time Series Construction**

### 6.1 Aggregation
- Aggregated `average_score` per `date_submitted`
- Median values were taken for categorical variables

### 6.2 Truncation
- Limited the analysis to the **first 210 days** due to sparse entries afterward.

### 6.3 Anomaly Detection
- **Isolation Forest** used to identify and remove 11 score anomalies.
- **Forward fill** used to handle missing values caused by outlier removal.

---

## 🧪 7. **Stationarity Check**

| Test   | Statistic | p-value | Result               |
|--------|-----------|---------|----------------------|
| ADF    | -2.33     | 0.16    | Non-stationary       |
| KPSS   | 1.34      | 0.01    | Non-stationary       |

**➡ First-order differencing** applied → Tests repeated:

| Test   | Statistic | p-value | Result          |
|--------|-----------|---------|-----------------|
| ADF    | -15.42    | ≈ 0     | Stationary ✅    |
| KPSS   | 0.165     | > 0.1   | Stationary ✅    |

---

## 🔄 8. **SARIMAX Modeling**

### 8.1 Model Configuration
- **Non-seasonal Order (p,d,q)**: (2, 1, 2)
- **Seasonal Order (P,D,Q,s)**: (2, 1, 2, 30)
- **Exogenous variables**: `age_band_encoded`, `highest_education_encoded`, `assessment_type_encoded`

### 8.2 Model Performance
| Metric | Value   |
|--------|---------|
| **MSE**    | 8.78    |
| **MAE**    | 2.38    |
| **RMSE**   | 2.96    |

- **Significant predictor**: `assessment_type_encoded` (p < 0.001)

### 8.3 Forecast Visualization
Forecast closely tracked the test data, confirming good fit.

---

## 🤖 9. **LSTM Model**

### 9.1 Preprocessing
- **Features**: `average_score` + 4 encoded variables
- **Sequence length**: 30 days
- **Scaler**: MinMaxScaler

### 9.2 Model Architecture
- LSTM(100) → Dropout(0.2)
- LSTM(50) → Dropout(0.2)
- Dense(1)

### 9.3 Performance
| Metric | Value   |
|--------|---------|
| **MSE**    | 18.80   |
| **MAE**    | 3.81    |
| **RMSE**   | 4.34    |

### 9.4 Evaluation
While LSTM was stable, **SARIMAX performed better**, likely due to limited data size and clearer time dependencies.

---

## ✅ 10. **Key Findings**

- **Assessment type** is a strong indicator of performance.
- **Final result and highest education** level offer predictive power.
- **Demographic data** (region, gender, etc.) provided limited value.
- **SARIMAX** outperforms **LSTM** for this use case.

---


## 📌 11. Summary

This project demonstrates a complete pipeline from **Big Data processing** with Spark, through **feature engineering and EDA**, to **advanced modeling** using **time series techniques** and **neural networks**.

We show that **student performance can be forecasted** with reasonable accuracy using minimal but well-selected features, and that **simple time-series models like SARIMAX** can outperform more complex deep learning approaches in limited-data scenarios.

---

