# B2B Sales Forecasting

![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)
![XGBoost](https://img.shields.io/badge/XGBoost-189FDD)
![LightGBM](https://img.shields.io/badge/LightGBM-9ACD32)
![Optuna](https://img.shields.io/badge/Optuna-2C5BB4)

A machine-learning pipeline that forecasts **10 months of sales for each of 14 business units** of an industrial technology company (Siemens). It replaces a manual, error-prone forecasting process. Historical sales are combined with global macroeconomic indicators.

## Problem

Monthly sales forecasts were built by hand. That was slow, depended on one person's judgement, and was prone to human error. Better forecasts feed directly into operations, and the business goals were to:
- Reduce **Days on Hand** by 2%.
- Increase **gross margin** by 1 percentage point.
- Reduce the **Lost Sales Ratio** by 1%.
- Avoid inventory shrinkage.

## Approach

All steps are in [`sales_forecasting.ipynb`](sales_forecasting.ipynb).

**1. Data understanding**
- Monthly sales (`Sales_EUR`) per business unit.
- Macroeconomic indicators:
  - Production and shipment indexes for machinery and electrical equipment across CN, FR, DE, IT, JP, CH, UK, US and EU.
  - World commodity prices: metals, energy, natural gas, crude oil and copper.
  - Producer prices and EUR/USD.

**2. Data preparation**
- Split the data by business unit.
- Added an external **COVID-19 deaths per million** feature to capture the pandemic shock.
- Dropped market indicators with incomplete history.

**3. Modelling**
- One pipeline per business unit and per algorithm: `StandardScaler`, then `SelectKBest(f_regression)` keeping the **20 best features**, then hyperparameter tuning with **Optuna**.
- Algorithms: **XGBoost**, **LightGBM** and **SVR**.

**4. Evaluation**
- The last 10 months were held out as validation.
- The best model for each business unit was chosen by **RMSE**.
- The top features per unit were analysed for interpretability.

**5. Deployment**
- The selected models were retrained and used to produce the final 10-month forecast.
- Forecasted market indicators were used as future inputs.

## Results

Mean validation RMSE across all business units:

| Model | Mean RMSE (EUR) |
|-------|-----------------|
| SVR | ~972k |
| LightGBM | ~766k |
| **XGBoost** | **~690k** |

XGBoost was the strongest model overall. Choosing the best model per business unit lets each unit use the algorithm that fits its own sales dynamics. The most predictive features differed between units, with production indexes and commodity prices among the strongest signals.

## Repository structure

```
b2b-sales-forecasting/
├── sales_forecasting.ipynb   # EDA, preparation, per-unit modelling, forecast
├── requirements.txt
└── README.md
```

> **Note:** The datasets are proprietary and are not included in this repository.

## Setup

```bash
pip install -r requirements.txt
```

Place the input files in a `data/` folder next to the notebook:
- `Case2_Sales data.csv`
- `Case2_Market data.xlsx`
- `Case2_Test Set Template.csv`
- `forecast_results_merged2.csv`
- `Covid deaths.csv`

Then run all cells. The forecast is written to `final_prediction.csv`.

## Tech stack

Python · pandas · NumPy · scikit-learn · XGBoost · LightGBM · Optuna · Matplotlib · Seaborn

## Author

**Vinícius Pinto** · [GitHub](https://github.com/vilallo)
