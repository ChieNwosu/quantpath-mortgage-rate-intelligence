# Phase 2: R Time-Series Analysis Layer

**Status**: 🔲 Planned  
**Timeline**: Summer 2026  
**Tech Stack**: R, tidyverse, forecast, tsibble, fable, ggplot2

---

## Overview

Phase 2 will extend the Phase 1 Excel workbook with advanced time-series analysis, connecting to Applied Time Series Analysis coursework at North Carolina Central University (NCCU).

This phase is **not yet implemented**. This folder is a placeholder for future R scripts, models, and documentation.

---

## Planned Objectives

- Ingest FRED/Freddie Mac MORTGAGE30US series into R
- Perform exploratory data analysis (EDA): trend, seasonality, autocorrelation
- Build ARIMA/ETS forecasting models for mortgage rate prediction
- Detect structural breaks (e.g., 2008 financial crisis, COVID-19, Fed rate hikes)
- Evaluate forecast accuracy (RMSE, MAE, MAPE)
- Visualize historical trends, forecast intervals, and residual diagnostics
- Export forecasts to CSV for Excel import
- Document model selection rationale and forecast interpretation

---

## Planned Business Questions

6. What are the historical trends and structural breaks in the 30-year fixed mortgage rate series?
7. Can we forecast mortgage rates 3–12 months ahead with reasonable accuracy?
8. How do forecast intervals inform refinance timing decisions?

---

## Planned Tech Stack

- **Data ingestion**: `fredr` package for FRED API access
- **Time-series modeling**: `forecast`, `fable`, `tsibble`
- **Visualization**: `ggplot2`, `plotly`
- **Reporting**: R Markdown or Quarto for reproducible analysis

---

## Planned Folder Structure

```
phase2_r/
├── README.md                  # This file
├── scripts/
│   ├── 01_data_ingestion.R    # FRED data download and preprocessing
│   ├── 02_eda.R               # Exploratory data analysis
│   ├── 03_arima_model.R       # ARIMA model fitting and forecasting
│   ├── 04_ets_model.R         # ETS model fitting and forecasting
│   ├── 05_structural_breaks.R # Structural break detection
│   └── 06_forecast_export.R   # Export forecasts to CSV
├── models/
│   ├── arima_model.rds        # Saved ARIMA model object
│   └── ets_model.rds          # Saved ETS model object
├── output/
│   ├── forecast_3mo.csv       # 3-month forecast
│   ├── forecast_12mo.csv      # 12-month forecast
│   └── plots/                 # Visualization outputs
└── docs/
    ├── model_selection.md     # Model selection rationale
    └── forecast_guide.md      # Forecast interpretation guide
```

---

## Planned Deliverables

- [ ] R scripts for FRED data ingestion and preprocessing
- [ ] Exploratory data analysis (EDA): trend, seasonality, autocorrelation
- [ ] ARIMA/ETS forecasting models for mortgage rate prediction
- [ ] Structural break detection (e.g., 2008 financial crisis, COVID-19, Fed rate hikes)
- [ ] Forecast accuracy metrics (RMSE, MAE, MAPE)
- [ ] Visualization: historical trends, forecast intervals, residual diagnostics
- [ ] Integration with Excel workbook: export forecasts to CSV for Excel import
- [ ] Documentation: R script guide, model selection rationale, forecast interpretation

---

## Academic Context

This phase will be developed as part of **Applied Time Series Analysis** coursework at **North Carolina Central University (NCCU)**.

---

## Status Disclaimer

**This phase is planned but not yet implemented.** This folder is a placeholder to signal the multi-phase vision of the QuantPath Mortgage Rate Intelligence Stack. All content in this folder is aspirational and subject to change based on coursework requirements, technical constraints, and portfolio priorities.

---

## References

- **FRED MORTGAGE30US**: https://fred.stlouisfed.org/series/MORTGAGE30US
- **R forecast package**: https://pkg.robjhyndman.com/forecast/
- **R fable package**: https://fable.tidyverts.org/
- **fredr package**: https://github.com/sboysel/fredr
