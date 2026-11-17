# PM2.5 Forecasting and Early Warning for Chiang Mai

Forecasts daily PM2.5 in Chiang Mai 1 to 7 days ahead and turns the forecast into a simple alert level. Capstone project, Thammasat University, January to May 2026.

## Problem

Every burning season (February to April) Chiang Mai has some of the worst air in the world. Most public warnings react to today's reading, which is too late for schools, hospitals and tourists to plan. The goal was to answer two questions:

1. How far ahead can we forecast PM2.5 reliably?
2. Is an early warning worth it in money terms?

## Data

| Source | What it gives | Frequency |
|---|---|---|
| Open-Meteo Air Quality API | PM2.5, PM10 and other pollutants | hourly |
| Open-Meteo Weather API | wind, humidity, rain, boundary layer height | hourly |
| NASA FIRMS (MODIS) via Google Earth Engine | fire hotspots around Chiang Mai | daily |

After merging and aggregating hourly data to daily: **1,569 days** (2022 to 2026) for one location (18.79 N, 98.98 E).

## Method

1. **Collect and merge** the three sources, aggregate hourly to daily (mean, max, sum depending on the variable) and handle missing values.
2. **Feature engineering, 48 features**, for example:
   - PM2.5 and hotspot lags from 1 to 7 days
   - rolling averages, shifted by one day so the model never sees the day it is predicting
   - a stagnation index (low wind plus low boundary layer), an upwind hotspot score and a dry-day streak
   - month encoded as sine and cosine
3. **Model:** XGBoost with quantile regression at the 10th, 50th and 90th percentiles, so each forecast comes with a range, not just one number.
4. **Validation:** time-series cross-validation (always training on the past and testing on the future), compared with two baselines: tomorrow equals today (persistence) and the historical average for that day (climatology).
5. **Alerts:** four levels (None, Watch, Warning, Emergency) based on the chance of going over the safe limit. The threshold is tuned with the F2 score, which weighs missed alerts more heavily than false alarms.
6. **Economic value:** a cost-of-inaction estimate based on TDRI's figure of about 15.5 million THB per day in health, productivity and tourism losses.

## Results

- Day 1 is the most reliable horizon: **MAE 6.54 µg/m³**, 40 to 50% lower error than the persistence and climatology baselines.
- Error grows about 22% by day 2 and levels off around 40% from day 4, so forecasts beyond 3 days should be read as a trend only.
- The most important features (SHAP) are hotspot density, yesterday's PM2.5, the stagnation index and boundary layer height. Fires and weather that traps smoke matter more than anything else.

The full write-up (in Thai) is in `docs/PM25_Research_Report.docx`.

## How to run

```sh
pip install -r requirements.txt
jupyter notebook notebooks/pm25_capstone.ipynb
```

- The hotspot data needs a Google Earth Engine account. Set `GEE_PROJECT` in the notebook to your own project; the first run asks you to sign in.
- The Open-Meteo APIs need no key.
- The raw CSVs are not stored in this repo. The notebook reads them from `AQ_FILE`, `HS_FILE` and `MERGE_FILE`, which currently point to my local Desktop. Download the data from the three sources above for 2022 to 2026 and change those paths before running.

## Repo contents

```
notebooks/pm25_capstone.ipynb   full pipeline with outputs
docs/PM25_Research_Report.docx  research report (Thai)
requirements.txt
```

Tanakit Tuadpitak, Bachelor of Science in Digital Transformation and Innovation, Thammasat University
