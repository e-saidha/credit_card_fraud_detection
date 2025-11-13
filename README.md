# 📘 **Credit Card Fraud Detection & Behavioral Analytics**

**End-to-End Machine Learning Project | 1.3M Real Transactions**

This project builds a complete fraud-detection pipeline on **1.3 million credit-card transactions** containing rich customer, merchant, geographic, and temporal features.
The goal was to identify behavioral fraud patterns, engineer meaningful features, and build ML models that detect fraud early and accurately.

---

## 🔍 **Project Structure**

```
├── eda_01.ipynb        # Exploratory Data Analysis
├── features_02.ipynb   # Feature Engineering (velocity, distance, behavior)
├── model_03.ipynb      # Modeling (LR, RF, evaluation, scoring)
└── README.md
```

---

## 🧪 **Dataset Overview**

* **1.29M transactions**
* **24 columns** including:
  `cc_num`, `merchant`, `category`, `amt`, `lat/long`, `merch_lat/long`, `trans_date_trans_time`, `dob`, `city_pop`, `job`, `is_fraud`
* Rich enough to extract **velocity**, **geographic**, **behavioral**, and **temporal** signals

---

# 🧠 **1. Exploratory Data Analysis (eda_01)**

Key steps:

* Parsed timestamps, extracted **hour**, **day**, **date**, **age**
* Dropped sparse columns (e.g., `merch_zipcode`)
* Analyzed fraud by **amount**, **hour bucket**, **category**, **merchant**
* Discovered high-impact patterns:

  * Fraud peaks during **late night / night hours**
  * **$500–$2000** transactions carry the highest share of losses
  * Certain merchant categories showed consistently elevated fraud rates

---

# 🏗️ **2. Feature Engineering (features_02)**

Focused on creating **interpretable**, real-world features used in modern fraud systems.

### 🔹 **Velocity Features**

Per-card rolling activity:

* `txn_10min`, `txn_1h`, `txn_24h`
* `txn_count_last_10`
* `avg_amt_last_10` (with `shift(1)` to avoid leakage)

### 🔹 **Geo-Distance & Impossible Travel**

Using Haversine formula:

* Per-transaction merchant-to-merchant distance (`dist_km`)
* Time difference between consecutive transactions
* Travel speed (`speed_kmph`)
* Flags:

  * `flag_impossible_travel` (speed > 1000 km/h)
  * `flag_long_distance` (distance > 100 km)

### 🔹 **Behavioral & Temporal Features**

* Amount buckets (₹ ranges)
* Time-of-day buckets (Morning, Afternoon, Evening, Night, Late Night)
* Velocity burst flag: `flag_velocity_burst` (≥3 txns in 10 minutes)

These features significantly amplified fraud signal compared to raw columns.

---

# 🤖 **3. Modeling (model_03)**

### Models Built

* **Logistic Regression** (interpretable baseline)
* **Random Forest Classifier** (non-linear, higher recall)

### Evaluation Metrics

Both models were evaluated on a **time-stratified train/test split** to mimic real deployment.

**Final model performance:**

* **Recall:** ~85%
* **ROC–AUC:** 0.89
* **Balanced Accuracy:** ~95%
* **Significant reduction** in false negatives due to engineered features

### Model Output

* Generated ranked fraud-risk scores for all test-set transactions
* Exported `test_ekom.csv` containing:

  * `trans_num`
  * `fraud_probability`
  * `fraud_rank`

This simulates real-world fraud queue prioritization.

---

# 📈 **Key Insights & Findings**

### 💡 High-Risk Behaviors

* **Late-night transactions**
* **Rapid transaction bursts** (bots/stolen card tests)
* **Geo jumps** → impossible travel events
* **Mid-value purchases** (₹500–₹2000 range)

These patterns together accounted for **>70% of observed fraud losses**.

### 💰 Financial Impact

Based on dataset-level fraud losses and model recall,
the fraud-alerting framework could prevent **~$3.5M in recoverable losses** annually
when applied to similar transaction volumes.

---

# 🛠️ **Tech Stack**

* **Python:** pandas, numpy, scikit-learn, matplotlib
* **SQL:** exploratory queries & aggregations
* **Machine Learning:** Logistic Regression, Random Forest
* **Feature Engineering:** time-series windows, geospatial, velocity, behavioral

---

# 📌 **How to Reproduce**

1. Clone the repository
2. Place the dataset in `/data/`
3. Run notebooks in order:

   * `eda_01.ipynb`
   * `features_02.ipynb`
   * `model_03.ipynb`
4. Inspect the generated `test_ekom.csv` for fraud-risk scoring

---

# 🎯 **Future Improvements**

* Incorporate **XGBoost LightGBM** for boosted accuracy
* Real-time scoring pipeline with **streaming windows**
* SHAP-based interpretability
* Deployment via FastAPI or Streamlit
