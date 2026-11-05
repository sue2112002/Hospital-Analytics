# Hospital Analytics Notebooks

Python notebooks that turn hospital-operations questions into data analysis, simple decision rules and forecasts. Each project starts from a **user question** (who needs the answer and what decision it supports), then shows the method and the result.

> Built from university coursework labs ([add course name]). Where data is simulated, it is labelled as such below.

## Projects

| # | Question | Data | Main tools |
|---|----------|------|------------|
| 1 | Which discharged diabetic patients should get follow-up first? | Real: 101,766 hospital encounters (Kaggle "Diabetes Readmission" dataset) | pandas, scikit-learn, matplotlib |
| 2 | How full will hospital beds be next week, and where does patient flow stall? | Real: CDC weekly hospital respiratory data 2020–2024 (forecasting). Simulated: ED flow and 90-day bed use | pandas, TensorFlow/Keras (LSTM), seaborn |
| 3 | Is a pharmacy / imaging process stable, capable and wasting time? | Simulated | NumPy, SciPy, matplotlib (SPC, capability, value-stream mapping) |
| 4 | Are stored chemicals at the right temperature and used in the right order? | Simulated sensor readings and sample records | SQLite (SQL), Python, seaborn |
| 5 | Where should hospital departments be placed to minimise patient/staff movement? | Small illustrative flow and distance matrices | NumPy, itertools, seaborn |

---

### 1. Readmission risk scoring — `01_readmission_risk_scoring/`
- Built a transparent 0–4 risk score from four rules (stay of 7+ days, 2+ prior inpatient visits, any emergency visit, 10+ medications).
- 30-day readmission rate rises from **7.0 % (score 0)** to **22.6 % (score 4)**.
- Grouped scores into Low / Medium / High tiers (8.9 % / 15.3 % / 22.6 % readmission) and attached a follow-up action to each tier (e.g. review within 48 h for the 894 High-risk patients).
- Compared with a Random Forest baseline (ROC-AUC 0.63 on the held-out set).
- **Data:** download `diabetic_data.csv` from the Kaggle dataset linked in the notebook and place it next to the notebook.
- **Limitations:** thresholds were chosen by hand; the ML model uses only six numeric features and is a baseline, not a validated clinical tool.

### 2. Bed occupancy forecasting and patient-flow analysis — `02_bed_occupancy_forecasting_lean_flow/`
- Task 1: ED patient journey (simulated, 100 patients). Identified *Waiting for Doctor* as the main bottleneck and proposed Lean fixes.
- Task 2: 90 days of simulated bed use; occupancy rate against a 90 % threshold.
- Task 3: LSTM forecasting of weekly inpatient bed occupancy from CDC data (6-week look-back, 80/20 chronological split, RMSE and MAE reported in the notebook).
- **Data:** `raw_weekly_hospital_respiratory_data_2020_2024.csv` from the CDC (add link) in the notebook folder.
- **Limitations:** the scaler is fitted on the full series before the train/test split (minor leakage); the CDC file has several rows per week, so aggregate by week before counting weeks.

### 3. Process quality: SPC, capability and value-stream mapping — `03_process_quality_spc_lean/`
- X-bar/R control charts detected a 3-minute shift in preparation time on days 21–25.
- Capability analysis: Cp 0.52, Cpk 0.22. Normal-theory defect estimate (259,612 DPMO) overstated the true rate (220,500 DPMO) for skewed wait-time data.
- Value-stream map: 38 % value-added ratio; largest wait is radiologist review (~19.7 min). Simulating a 40 % cut in that wait lowers cycle time from 56.2 to 48.4 min.
- All data simulated.

### 4. Cold-chain monitoring with SQL — `04_cold_chain_monitoring_sql/`
- `cold_chain_sql_inventory_monitoring.ipynb`: SQLite schema (chemicals, rooms, temperature readings, usage), earliest-expiry-first selection, expiry and 2–8 °C violation reports, heatmap and trend plots.
- `cold_storage_alarm_detection.ipynb`: simulation of 10 shelves with an injected "door left open" event; compares threshold, statistical and rate-of-change alarms.
- **Note:** the alarm notebook follows a provided lab structure; my work is the implementation and analysis. Re-running the SQL notebook re-inserts the sample rows (add a `UNIQUE` constraint or reset the tables first).

### 5. Facility layout optimisation — `05_facility_layout_optimisation/`
- Brute-force quadratic-assignment search for ICU / Radiology / Emergency / Surgery placement, plus a what-if run with one department fixed in place and the cost difference reported.

## Running
Open any notebook in Colab or Jupyter, install the imports listed in its first cells, and place any required CSV beside it.
