# AI-Powered Demand Forecasting & Inventory Optimization

An end-to-end machine learning project that forecasts product demand from historical sales data and converts those forecasts into practical inventory replenishment recommendations.

## Problem Statement

Accurate demand forecasting helps businesses reduce stockouts and avoid unnecessary inventory. This project uses historical grocery sales data to predict future demand for a selected store-product combination and applies the forecast to calculate safety stock, reorder points, and recommended order quantities.

## Project Workflow

```text
Historical Sales Data
        ↓
Data Understanding & EDA
        ↓
Time-Series Feature Engineering
        ↓
Naive Forecasting Baseline
        ↓
Random Forest
        ↓
LightGBM
        ↓
Model Evaluation
        ↓
MLflow Experiment Tracking
        ↓
Demand Forecast
        ↓
Safety Stock
        ↓
Reorder Point
        ↓
Inventory Replenishment Recommendation
```

## Dataset

The project uses the Corporación Favorita Grocery Sales Forecasting dataset.

The main training file contains historical sales records with:

- Date
- Store number
- Product number
- Unit sales
- Promotion information

Due to the large size of the original dataset, the raw `train.csv` file is not included in this repository.

## Data Analysis

A selected store-product combination was analyzed to understand its daily demand behavior.

The analysis included:

- Daily sales trends
- Missing-date analysis
- Rolling averages
- Weekly seasonality
- Demand variability
- Actual vs predicted demand

The analysis identified noticeable weekly demand patterns, with higher average sales observed toward the end of the week for the selected product.

## Feature Engineering

Time-series features were created from historical demand:

- Day of week
- Day of month
- Month
- Year
- Lag 1
- Lag 7
- Lag 14
- 7-day rolling average
- 14-day rolling average
- 30-day rolling average

Rolling features were calculated using previous observations to avoid target leakage.

## Machine Learning Models

Three forecasting approaches were evaluated:

### 1. Naive Baseline

A simple weekly-lag baseline was created using demand from the previous week.

### 2. Random Forest

A Random Forest Regressor was trained as a tree-based baseline model.

### 3. LightGBM

LightGBM was used as the primary gradient boosting model for demand forecasting.

A separate hyperparameter experiment was also conducted to evaluate whether a more complex configuration improved performance.

## Model Performance

Evaluation was performed using a chronological train-test split.

| Model | MAE | RMSE |
|---|---:|---:|
| Naive Baseline | 2.0211 | 2.9580 |
| Random Forest | 1.5519 | 2.1138 |
| LightGBM | **1.5172** | 2.1519 |
| Tuned LightGBM | 1.5964 | 2.2278 |

The baseline LightGBM model achieved the lowest MAE.

Compared with the naive baseline, it reduced MAE by approximately 24.9%.

Random Forest achieved the lowest RMSE, showing stronger performance on larger prediction errors.

The tested tuned LightGBM configuration did not improve the baseline model, so the original LightGBM configuration was retained as the selected model.

## MLflow Experiment Tracking

MLflow was used to track machine learning experiments.

Tracked information includes:

- Model parameters
- MAE
- RMSE
- Experiment runs
- Trained LightGBM model artifact

Experiments include:

- `random_forest_baseline`
- `lightgbm_baseline`
- `lightgbm_tuned`
- `lightgbm_best_model`

## Inventory Optimization

The forecasting model was connected to an inventory decision layer.

The system calculates:

### Safety Stock

Safety stock is used to protect against demand variability during the replenishment lead time.

### Reorder Point

The reorder point combines expected lead-time demand with safety stock.

For the selected product:

- Forecasted daily demand: **1.66 units**
- Assumed lead time: **7 days**
- Safety stock: **13.12 units**
- Forecast-based reorder point: **24.76 units**

Therefore, inventory reaching approximately 25 units triggers a replenishment recommendation.

## Inventory Recommendation

The system compares current inventory against the forecast-based reorder point.

Example:

| Current Inventory | Reorder Point | Recommended Order | Action |
|---:|---:|---:|---|
| 10 | 24.76 | 27 | REORDER |
| 15 | 24.76 | 22 | REORDER |
| 20 | 24.76 | 17 | REORDER |
| 25 | 24.76 | 0 | NO REORDER |
| 30 | 24.76 | 0 | NO REORDER |

The order quantity is calculated using a 14-day order-up-to horizon.

## Technologies Used

- Python
- Pandas
- NumPy
- Matplotlib
- Scikit-learn
- LightGBM
- MLflow
- Joblib
- Jupyter Notebook
- Git & GitHub

## Project Structure

```text
demand-forecasting-inventory/
│
├── dashboard/
│
├── data/
│   └── .gitkeep
│
├── models/
│   └── lgb_demand_forecasting_model.pkl
│
├── notebooks/
│   └── 01_data_understanding.ipynb
│
├── src/
│
├── .gitignore
├── README.md
└── requirements.txt
```

## Model Usage

The trained LightGBM model is saved in:

```text
models/lgb_demand_forecasting_model.pkl
```

It can be loaded using Joblib:

```python
import joblib

model = joblib.load(
    "models/lgb_demand_forecasting_model.pkl"
)
```

## Future Improvements

Potential extensions include:

- Incorporating store and product metadata
- Adding promotions and holiday features
- Incorporating oil-price information
- Forecasting multiple store-product combinations
- Automated hyperparameter optimization
- Power BI inventory dashboard
- Automated model retraining
- Deployment as a forecasting API

## Key Takeaway

This project demonstrates an end-to-end approach to combining machine learning demand forecasting with inventory optimization.

Instead of stopping at demand prediction, the forecast is converted into an actionable replenishment decision using safety stock and reorder-point calculations.
