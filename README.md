# Predictive Maintenance Analytics: Industrial Equipment Failure Prediction
 
Bridging mechanical engineering domain knowledge with data analytics: a synthetic sensor fleet, a failure-prediction ML pipeline, and a 4-page Power BI dashboard for fleet health monitoring.
 
![Status](https://img.shields.io/badge/status-complete-brightgreen) ![Python](https://img.shields.io/badge/Python-3.12-blue) ![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-yellow)
 
## Overview
 
Unplanned equipment failure is one of the most expensive events in industrial operations and reactive maintenance typically costs several times more than planned maintenance, and unplanned downtime halts production. This project simulates a realistic sensor-instrumented fleet of 19 industrial units and builds an end-to-end system to flag at-risk equipment **7 days before failure**, with results feeding a Power BI dashboard built for fleet managers and maintenance planners.
 
**What makes this different from a typical portfolio ML project:** the synthetic dataset isn't random noise with labels slapped on, it's built on realistic degradation physics (vibration and temperature climbing on an accelerating curve in the weeks before failure, current draw rising with mechanical resistance, pump pressure dropping under cavitation), the same signatures used in real industrial condition monitoring.
 
## Tech Stack
 
`Python` (pandas, numpy, scikit-learn) · `Power BI` (DAX, data modeling) · `Random Forest Classifier`
 
## Dataset
 
Synthetic but physics-informed 41,363 sensor readings across:
- **19 equipment units**, 4 types: Centrifugal Pumps, Induction Motors, Air Compressors, Conveyor Motors
- **18-month simulation window** (Jan 2025 – Jun 2026), readings every 6 hours
- **148 failure events** across 8 distinct failure modes: Bearing Wear, Cavitation, Seal Failure, Misalignment, Overheat, Winding Overheat, Belt Slippage, Valve Failure
Each failure is preceded by a **14–28 day degradation ramp** rather than appearing as a random spike, so the resulting model learns genuine pre-failure signatures instead of curve-fitting to noise.
 
**Sensor features:** vibration (mm/s RMS), temperature (°C), current draw (A), RPM, pressure (bar — pump/compressor units only), ambient temperature/humidity, hours since last maintenance.
 
## Feature Engineering
 
Raw instantaneous sensor readings are weak predictors on their own, what matters is the **trend**. For each unit, rolling-window features were engineered:
- 3-day and 7-day rolling mean and standard deviation per sensor
- 3-day slope (rate of change) per sensor, the single strongest predictive signal
- Time-since-maintenance as a wear-accumulation proxy
## Model
 
**Random Forest Classifier** predicting failure within a 7-day window, using a **time-aware train/test split** (last 20% of the timeline held out, not a random shuffle) to avoid leaking future information into training.
 
### Results
 
| Metric | Value |
|---|---|
| ROC-AUC | 0.9985 |
| Recall (failure class) | 0.97 |
| Precision (failure class) | 0.89 |
| F1 Score | 0.928 |
 
Recall was prioritized in interpreting these results, a missed failure (false negative) is far more costly in a maintenance context than a false alarm. The model catches 799 of 825 pre-failure windows in the test set.
 
**Top predictive features:** current vibration level, 3-day vibration slope, 3-day temperature slope, and rolling vibration volatility consistent with real-world condition-monitoring literature, where vibration trend is the leading indicator of mechanical degradation.
 
## Power BI Dashboard
 
A 4-page dashboard built for fleet-level and unit-level decision-making, dark industrial theme (navy/charcoal background with blue, green/amber/red risk accents).
 
**1. Fleet Overview**: Fleet-wide KPIs (unit count, units at high risk, fleet health score, failure events), equipment mix, risk distribution, and a top-5 highest-risk-units table.
<img width="1299" height="737" alt="Screenshot (1127)" src="https://github.com/user-attachments/assets/5d9a7b8c-ca6d-415e-87fa-87f3abd6093f" />

 
**2. Equipment Deep Dive**: Unit-level drill-down via slicer: live sensor trend charts (vibration, temperature, current, RPM) with danger-zone reference lines, days-to-predicted-failure, and predicted failure mode.
<img width="1304" height="735" alt="Screenshot (1128)" src="https://github.com/user-attachments/assets/c032727a-0973-49c8-a0cd-7b28c2607b72" />

 
**3. Failure Risk & Predictions**: Fleet-wide risk ranking matrix, risk-vs-maintenance-recency scatter plot, failure mode breakdown, and risk score by equipment type.
<img width="1297" height="723" alt="Screenshot (1129)" src="https://github.com/user-attachments/assets/de7b4fe4-084c-4524-9f8d-6909fd3af01b" />

 
**4. Maintenance Impact**: Mean Time Between Failures (MTBF), estimated cost avoidance from a predictive vs. reactive maintenance strategy, and failure trend over time.
<img width="1302" height="731" alt="Screenshot (1130)" src="https://github.com/user-attachments/assets/90577de8-fb6e-4f00-b50f-4573d7f4a595" />

 
**DAX highlights:** correcting for "all-time history" bias in risk measures by evaluating only each unit's *latest* sensor reading (via a `Latest Reading Per Unit` summarized table) rather than averaging across 18 months of data; a mechanically-correct MTBF calculation (total unit-operating-days ÷ failure count, not a naive fleet-wide average); conditional formatting rules applied consistently across cards, tables, and matrices using a shared red/amber/green risk-band threshold (≥0.7 critical, 0.4–0.69 watch, <0.4 healthy).
 
> **Note on cost figures:** Maintenance Impact page cost estimates ($15K reactive / $4K planned per event) are illustrative industry-pattern assumptions for demonstration purposes, not sourced from actual maintenance records.
 
## Project Structure
 
```
predictive-maintenance/
├── generate_dataset.py          # synthetic sensor data generator with degradation physics
├── train_model.py               # feature engineering, model training, evaluation
├── sensor_data.csv              # raw generated sensor readings
├── scored_sensor_data.csv       # sensor data + model failure risk scores (Power BI source)
├── feature_importance.csv       # ranked feature importances
├── failure_prediction_model.pkl # trained model artifact
├── predictive_maintenance.pbix  # Power BI dashboard file
└── README.md
```
 
## How to Reproduce
 
```bash
pip install pandas numpy scikit-learn joblib
python generate_dataset.py   # generates sensor_data.csv
python train_model.py        # generates scored_sensor_data.csv + trained model
```
Then open `predictive_maintenance.pbix` in Power BI Desktop and refresh the data source to point at `scored_sensor_data.csv`.
 
## Skills Demonstrated
 
- Domain-informed synthetic data generation (mechanical engineering degradation physics)
- Time-series feature engineering (rolling windows, rate-of-change/slope features)
- Time-aware model validation (avoiding data leakage in sequential data)
- Classification model evaluation with business-context metric prioritization (recall over accuracy)
- DAX data modeling for "current state vs. historical" measure correctness
- Dashboard design and information architecture across multiple analytical views
## Author
 
**Adeoye Boluwatife Emmanuel (Ballistic)**
B.Tech Mechanical Engineering, LAUTECH · Data Analytics
 
[Portfolio](https://ballistic-02.github.io) · [GitHub](https://github.com/Ballistic-02) · [LinkedIn](https://linkedin.com/in/bea2002)
