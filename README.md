# Time Series Analysis of Student Performance

This repository contains an end-to-end analysis and modeling workflow for a dataset related to student performance and demographics. The analysis involves preprocessing, exploratory data analysis, time series analysis, and the implementation of SARIMAX and LSTM models for forecasting.

---

## **Project Overview**

### **Objective**
Analyze the relationships between various factors influencing student performance, preprocess the data, and build predictive models to forecast scores based on demographic and time-related features.

### **Key Libraries Used**
- **Data Processing**: PySpark, Pandas
- **Visualization**: Matplotlib, Seaborn
- **Statistical Analysis**: Statsmodels
- **Machine Learning**: Scikit-learn, TensorFlow

---

## **Workflow**

### **1. Data Overview and Preprocessing**

#### **Dataset Description**
The project involves merging and analyzing data from multiple CSV files:
- `courses.csv`
- `assessments.csv`
- `studentInfo.csv`
- `studentRegistration.csv`
- `studentVle.csv`
- `vle.csv`

#### **Preprocessing Steps**
- Removed columns with excessive missing data (`week_from`, `week_to`).
- Dropped rows with missing values in critical columns.
- Encoded categorical variables:
  - **Highest Education**: Binary (Post Graduate Qualification: 1, Others: 0).
  - **Age Band**: Binary (`55<=`: 1, Others: 0).
  - **Final Result**: Fail: 0, Withdrawn: 0, Pass: 1, Distinction: 2.
  - **Assessment Type**: Exam: 0, TMA: 1, CMA: 2.
- Dropped insignificant columns like `gender`, `region`, and `imd_band` after statistical analysis.

---

### **2. Exploratory Data Analysis**

#### **Visualizations**
- Correlation heatmap for numerical variables.
- Boxplots for categorical variables like gender, region, and assessment types.
- Time-series trend visualization of average scores.

#### **Insights**
- No significant linear correlations between numerical predictors and scores.
- Some demographic variables (e.g., Highest Education, Age Band) influenced scores and were encoded.

---

### **3. Time Series Analysis**

#### **Stationarity Testing**
- **ADF Test**: p-value > 0.05 (non-stationary).
- **KPSS Test**: p-value < 0.05 (non-stationary).
- **Solution**: First-order differencing applied to make the series stationary.

#### **Seasonal Component Analysis**
- Seasonal differencing with a period of 30 days.
- Seasonal ACF and PACF analysis to determine seasonal parameters for modeling.

---

### **4. Modeling**

#### **SARIMAX Model**

- **Data Preparation**:
  - Aggregated scores by `date_submitted`.
  - Included exogenous variables: `highest_education_encoded`, `age_band_encoded`, `assessment_type_encoded`.

- **Parameters**:
  - SARIMAX order: `(2, 1, 2)`
  - Seasonal order: `(2, 1, 2, 30)`

- **Evaluation Metrics**:
  - Mean Squared Error (MSE): Low.
  - Root Mean Squared Error (RMSE): Low.

- **Key Insight**: Captured trend and seasonality effectively.

#### **LSTM Model**

- **Preprocessing**:
  - Normalized data using MinMaxScaler.
  - Created sequences of 30 timesteps for input.

- **Model Architecture**:
  - Two LSTM layers with Dropout for regularization.
  - Dense output layer for predicting `average_score`.

- **Evaluation Metrics**:
  - MSE: Moderate.
  - RMSE: Moderate.

- **Key Insight**: Captured non-linear patterns but required careful tuning.

---

### **5. Results and Comparisons**

| Metric         | SARIMAX | LSTM |
|----------------|---------|------|
| **MSE**       | Low     | Moderate |
| **RMSE**      | Low     | Moderate |

---

## **Conclusions**

1. **SARIMAX**:
   - Provided interpretable insights into seasonal and trend components.
   - Suitable for datasets with significant seasonal patterns.

2. **LSTM**:
   - Captured non-linear relationships effectively.
   - Slightly less accurate compared to SARIMAX.

---

## **Future Work**

1. Experiment with advanced models like Bidirectional LSTMs or Transformers.
2. Validate models using additional validation datasets or cross-validation.
3. Include additional features or engineered metrics for improved accuracy.

---

## **How to Run**
1. Clone this repository:
   ```bash
   git clone https://github.com/your-repo-name/time-series-analysis.git
   ```
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Run the analysis:
   ```bash
   python main.py
   ```

---
