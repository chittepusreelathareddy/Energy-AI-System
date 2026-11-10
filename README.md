# Energy-AI-System
AI-based electricity consumption forecasting, anomaly detection and optimization with a FastAPI backend and React dashboard.
# AI-Based Energy Consumption Prediction and Optimization

REVA University, Center for AI & ML (Major Project, Phase-1)
Guide: Ms. Shweta Salimath

A system that forecasts the next 24 hours of electricity consumption from hourly
smart-meter data, flags abnormal readings, and turns the forecast into plain-language
recommendations (peak-shift advice and budget alerts), shown on a web dashboard.

## Features
- Data cleaning: hourly re-indexing, duplicate removal, interpolation, input validation
- Feature engineering: calendar, lag (1/24/168 h), 24 h rolling mean, temperature
- Models: Random Forest, XGBoost and an optional neural network (MLP), compared on a
  chronological 80/20 split (MAE, RMSE, MAPE, R2). The lowest-MAPE model is selected automatically
- 24-hour recursive forecast
- Anomaly detection: residual threshold (mean + 3 standard deviations) with low/medium/high severity
- Optimization: peak-shift and daily-budget recommendations
- REST API (FastAPI) with device-key authentication, SQLite storage (SQLAlchemy)
- React + Chart.js dashboard

## Project structure
```
energy_core.py      core library: preprocessing, features, models, forecast, anomalies, recommendations
api.py              FastAPI app (readings, train, forecast, anomalies, recommendations, metrics)
dashboard.html      React + Chart.js dashboard, served at /
tests/              pytest test cases (valid, invalid, boundary, error)
```

## Setup
```bash
pip install fastapi uvicorn sqlalchemy joblib pandas scikit-learn xgboost httpx pytest
uvicorn api:app --reload
```
Open http://localhost:8000/ for the dashboard and http://localhost:8000/docs for the API.
Set `DEVICE_API_KEY` before real use (default: `device-secret-123`).

## Usage
1. `POST /readings/bulk` with at least 14 days (336 hourly readings), header `X-API-Key`
2. `POST /train` to benchmark the models and select the best one
3. `GET /forecast`, `GET /anomalies`, `GET /recommendations?budget=30`

## Results (synthetic data)
| Model | MAPE | R2 |
|---|---|---|
| XGBoost (selected) | 5.08% | 0.917 |
| Random Forest | 5.38% | 0.908 |
| Neural MLP | 6.58% | 0.871 |

Testing: 60 test cases across 9 modules, all passing after fixes.

## Limitations
- The dataset is synthetic; real smart-meter data (for example UCI Household Power
  Consumption or Ausgrid) has not been used yet
- MAPE is a one-step-ahead score, not a true 24-hour multi-step score
- Anomaly detection raises about 2% false alarms on normal data
- The optional neural model is an MLP; SARIMAX and LSTM/GRU are planned for Phase-2
- The dashboard has not yet been tested in a browser

## Team
Sreelatha (R23EK058), Surendra (R23EK059), Guru Nitish (R23ED114), P Chandrakanth Reddy (R23ED107)
