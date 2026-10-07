# Zomato Food Delivery Analysis
### AICTE | IBM SkillsBuild Data Analytics with AI Internship 2026

**Author:** Ramanjot Kaur  
**Programme:** AICTE | IBM SkillsBuild Data Analytics with AI (2026)

---

## Project Overview

This project applies an end-to-end Data Analytics and AI/ML workflow to a synthetic educational dataset modelled on a food delivery platform. The central goal is **late delivery prediction**: identifying which operational and order-level factors cause delivery delays, and building a machine learning classifier to flag high-risk orders before they happen.

> **Note:** This dataset is a *synthetic academic dataset based on a Zomato-like food delivery context*, created for educational purposes. It is not proprietary Zomato operational data.

---

## Business Problem

> **Can we predict whether a food delivery order will be late, and which factors drive late deliveries?**

Late deliveries drive negative ratings, cancellations, and customer churn. A reliable prediction model allows the platform to:
- Proactively allocate delivery partners to high-risk orders
- Enforce preparation time SLAs with restaurants
- Staff up during high-risk meal periods
- Implement distance-based order routing policies

---

## Dataset Description

| Property | Value |
|----------|-------|
| Source | `Zomato Cleaned Datset RK.xlsx` — synthetic educational dataset
https://docs.google.com/spreadsheets/d/17wRyHpqDP4osTlq96HNqq0rcEfDvKdhZ/edit?usp=drive_link&ouid=100535993597017051372&rtpof=true&sd=true |
| Records | 10,000 orders (after cleaning from 10,045 raw records) |
| Columns | 38 (33 raw measurement columns + 5 computed fields) |
| Missing values | 0 |
| Duplicate Order_IDs | 0 |
| Date range | April 2024 – March 2026 |
| Cities | 14 Indian cities (Mumbai, Delhi, Bengaluru, Hyderabad, Chennai, Pune, Kolkata, etc.) |
| Cuisines | North Indian, South Indian, Chinese, Italian, Fast Food, Healthy, Biryani, Desserts |

### Key Columns
`Order_Value_INR`, `Delivery_Distance_KM`, `Preparation_Time_Min`, `Estimated_Delivery_Min`, `Actual_Delivery_Min`, `Restaurant_Rating`, `Customer_Rating`, `Discount_Pct`, `Restaurant_Tier`, `Customer_Segment`, `Meal_Period`, `On_Time_Flag` (target)

### Cleaning Summary (from workbook documentation)
15 documented cleaning steps including: duplicate removal, missing value handling, text standardisation, date/time formatting, numeric conversion, validity checks (order values, discounts, charges, delivery times, ratings), outlier review, and computed field creation.

---

## Technologies

| Tool | Version | Purpose |
|------|---------|---------|
| Python | 3.14 | Core language |
| pandas | ≥2.0 | Data manipulation |
| numpy | ≥1.24 | Numerical operations |
| matplotlib | ≥3.7 | Visualisation |
| seaborn | ≥0.12 | Statistical plots |
| scipy | ≥1.10 | Statistical tests |
| scikit-learn | ≥1.3 | ML models, pipelines |
| openpyxl | ≥3.1 | Excel reading |
| jupyter | ≥1.0 | Notebook environment |
| IBM Bob | — | AI assistant for analysis, code review, recommendations |

---

## Setup & Run Instructions

### 1. Prerequisites
```
Python 3.9+
```

### 2. Install Dependencies
```bash
pip install -r requirements.txt
```

### 3. Place the Dataset
Ensure `Zomato Cleaned Datset RK.xlsx` is in the **same directory** as the notebook.

### 4. Run the Notebook
```bash
jupyter notebook Zomato_Food_Delivery_Analysis.ipynb
```
Then execute all cells in order (Kernel → Restart & Run All).

### 5. Or Execute Non-Interactively
```bash
jupyter nbconvert --to notebook --execute --inplace Zomato_Food_Delivery_Analysis.ipynb
```

---

## Project Structure

```
.
├── Zomato Cleaned Datset RK.xlsx      # Original dataset (do not modify)
├── Zomato_Food_Delivery_Analysis.ipynb # Complete executed notebook (source of truth)
├── requirements.txt                   # Python dependencies
├── Project_Report.docx                # Professional internship report
├── README.md                          # This file
└── fig_01_ontime_distribution.png     # Generated figures (created by notebook)
    fig_02_order_status.png
    fig_03_late_by_city.png
    fig_04_late_by_cuisine.png
    fig_05_delivery_scatter.png
    fig_06_order_value_dist.png
    fig_07_distance_dist.png
    fig_08_late_by_segment.png
    fig_09_preptime_boxplot.png
    fig_10_correlation_heatmap.png
    fig_11_confusion_matrices.png
    fig_12_roc_curves.png
    fig_13_feature_importance.png
```

---

## ML Methodology

### Target Variable
`is_late` — binary: 1 = Late (Actual_Delivery_Min > Estimated_Delivery_Min), 0 = On Time

### Leakage Prevention
Excluded from features: `Delivery_Variance`, `Actual_Delivery_Min` (post-event), `On_Time_Flag` (target), identifier columns, and derived band columns.

### Features (25 total)
- **Numeric (15):** Order_Value_INR, Discount_Pct, Discount_Amount_INR, Packaging_Fee_INR, Delivery_Charge_INR, Final_Amount_INR, Delivery_Distance_KM, Preparation_Time_Min, Estimated_Delivery_Min, Restaurant_Rating, Item_Count, Total_Expected_Time, Charge_to_Value_Ratio, Has_Discount, Order_Month
- **Categorical (10, label-encoded):** Meal_Period, Customer_Segment, Membership_Status, Order_Channel, Region, Restaurant_Tier, Cuisine, Payment_Mode, Distance_Bin, Offer_Type

### Train/Test Split
80% train (8,000) / 20% test (2,000), stratified by target, `random_state=42`

### Models

| Model | Accuracy | F1 Score (Late) | ROC-AUC | 5-Fold CV ROC-AUC |
|-------|----------|-----------------|---------|-------------------|
| Logistic Regression | 0.5615 | 0.6257 | 0.6139 | N/A |
| **Random Forest** | **0.6205** | **0.7198** | **0.6016** | **0.6122 ± 0.0103** |
| Naive Baseline (all Late) | 0.703 | 0.826 | 0.500 | — |

> The Random Forest outperforms Logistic Regression on accuracy and F1-score. The modest ROC-AUC (~0.61) reflects the inherent difficulty of the problem — no weather/traffic data is available, and 70.3% of orders are late (high base rate).

---

## Key Findings (from Executed Notebook)

| Metric | Value |
|--------|-------|
| Total orders | 10,000 |
| Late delivery rate | **70.3%** |
| Delivered orders | 9,081 (90.8%) |
| Cancelled orders | 745 (7.4%) |
| Mean Order Value | Rs 567.52 |
| Mean Delivery Distance | 4.22 KM |
| Mean Actual Delivery Time | 36.6 min |
| Mean Restaurant Rating | 3.94 / 5 |

### Statistical Tests
| Test | Variable | p-value | Significant? |
|------|----------|---------|--------------|
| t-test | Preparation Time (Late vs On Time) | 0.484 | No |
| t-test | Delivery Distance (Late vs On Time) | < 0.001 | **Yes** |
| Chi-square | Cuisine vs On Time Flag | 0.433 | No |

### Top Feature Importances (Random Forest)
Estimated Delivery Time, Delivery Distance, and Total Expected Time ranked as the top predictors of late delivery.

---

## IBM Bob's Role

IBM Bob was used at every stage of this project:

| Stage | Contribution |
|-------|-------------|
| Dataset Inspection | Parsed multi-sheet Excel workbook; extracted column definitions, distributions, and 15-step cleaning docs |
| Business Problem | Recommended late delivery prediction as the strongest problem supported by the data |
| Leakage Prevention | Identified Delivery_Variance and Actual_Delivery_Min as target-leaking variables |
| Feature Engineering | Suggested Total_Expected_Time, Charge_to_Value_Ratio, Distance_Bin |
| Model Architecture | Recommended Logistic Regression baseline + Random Forest with stratified CV |
| Code Review | Validated Pipeline structure, class weights, and split strategy |
| Report Generation | Generated Project_Report.docx and README.md from executed notebook results |

---

## Limitations

1. **Synthetic Data**: Educational dataset; findings may not transfer to real operations
2. **No Weather/Traffic**: Major real-world late delivery drivers are absent
3. **No Temporal Modelling**: Rush-hour cascade effects not captured
4. **Limited Tuning**: Reasonable defaults used; full grid search would improve results

---

## Licence / Disclaimer

This project is developed for the AICTE | IBM SkillsBuild Data Analytics with AI Internship 2026. The dataset is a synthetic academic dataset and is used for educational purposes only.
