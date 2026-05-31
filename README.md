# PM2.5 Forecasting & Early Warning System — Chiang Mai

End-to-end machine learning system that forecasts daily PM2.5 levels up to seven days ahead for Chiang Mai, Thailand. The system integrates multi-source environmental data through an automated ETL pipeline and uses XGBoost Quantile Regression to produce probabilistic forecasts with actionable confidence intervals.

## Overview

Chiang Mai faces severe PM2.5 pollution every burning season (February–April), causing significant health and economic damage. This project builds a forecasting system that not only predicts pollution levels but also quantifies the economic value of early warnings through a Cost-of-Inaction (COI) framework grounded in TDRI's economic baseline.

## Key Highlights

| Aspect | Detail |
|---|---|
| Data Coverage | 4 years (2022–2026) |
| Data Sources | Open-Meteo Air Quality, Open-Meteo Weather, NASA FIRMS satellite hotspots |
| Daily Records | 1,569 |
| Engineered Features | 48 |
| Forecast Horizon | 1 to 7 days ahead |
| Sweet Spot | Day 1 (MAE 6.54 µg/m³) |
| Performance vs Baselines | 40–50% MAE improvement vs Persistence and Climatology |

## Tech Stack

- **Language:** Python 3.10+
- **ML Framework:** XGBoost (with Quantile Regression objective)
- **Data Engineering:** Pandas, NumPy, Open-Meteo API, Google Earth Engine
- **Explainability:** SHAP
- **Visualization:** Matplotlib, Power BI

## Methodology

1. **ETL Pipeline** — Automated daily data ingestion from three sources:
   - Open-Meteo Air Quality API (hourly pollutants)
   - Open-Meteo Weather API (hourly meteorology)
   - NASA FIRMS via Google Earth Engine (daily MODIS hotspot detection)

2. **Data Aggregation** — Hourly to daily aggregation with appropriate functions (mean, max, sum, mode)

3. **Feature Engineering (48 features)** — Six groups including:
   - Lag features (PM2.5 and hotspots, 1–7 days back)
   - Rolling statistics with shift(1) to prevent data leakage
   - Domain-specific features (Stagnation Index, Upwind Hotspot Score, Dry-Day Streak)
   - Interaction features
   - Cyclic time encoding (month sin/cos)

4. **Modeling** — XGBoost Quantile Regression at q10, q50, q90 for prediction intervals

5. **Sweet Spot Analysis** — Time-series cross-validation across horizons to identify the maximum reliable forecast distance

6. **Alert System** — 4-tier alert framework (None / Watch / Warning / Emergency) based on Exceedance Probability with F2-optimized threshold tuning

7. **Economic Quantification** — Cost-of-Inaction (COI) framework using TDRI economic baseline (15.5M THB/day in PM2.5-related damages across healthcare, productivity, and tourism)

8. **Dashboard** — Tiered alert dashboard in Power BI presenting forecast levels, confidence ranges, and COI estimates for public health stakeholders

## Project Structure

```
pm25-forecasting/
├── notebooks/
│   └── pm25_capstone.ipynb           # Full capstone notebook with outputs
├── src/                              # Production scripts
├── docs/
│   └── PM25_Research_Report.docx     # Full research report (Thai)
├── requirements.txt                   # Python dependencies
└── README.md
```

## Key Insights

- **Day 1 is the Sweet Spot** with MAE of 6.54 µg/m³; error grows 22% by Day 2 and stabilizes at ~40% by Day 4
- **Top predictive features** identified via SHAP include hotspot density, lag PM2.5 (autocorrelation), stagnation index, and boundary layer height
- **Quantile Regression** produces actionable uncertainty bands rather than overconfident point estimates, well-suited for public health decisions
- **F2-optimized threshold** prioritizes recall over precision because in public health, missed alerts cost more than false alarms

## Author

**Tanakit Tuadpitak**
Bachelor of Science in Digital Transformation and Innovation
Thammasat University, Thailand
Capstone Research Project, 2026
