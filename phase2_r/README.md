# Phase 2: R Time-Series Analysis Layer

**Status**: 🔲 Planned  
**Timeline**: Summer 2026  
**Tech Stack**: R, tidyverse, tseries, ggplot2, fredr

---

## Overview

Phase 2 will extend the Phase 1 Excel workbook with an R-based time-series analysis layer focused on understanding the historical behavior and statistical properties of the MORTGAGE30US series.

This phase connects to **Applied Time Series Analysis** coursework at **North Carolina Central University (NCCU)** and **IBM's Data Analytics with Excel and R Professional Certificate** (Coursera).

> **Note:** The IBM certificate connection represents applied learning — not a completed IBM capstone project.

This phase is **not yet implemented**. This folder is a placeholder for future R scripts and documentation.

---

## Planned Focus Areas

| Analysis Area | Description |
|---|---|
| **Stationarity testing** | ADF, KPSS tests to assess whether the rate series is stationary or unit-root |
| **Rolling averages** | Moving averages to identify trend direction and smooth short-term noise |
| **Rolling volatility** | Windowed standard deviation to measure rate stability over time |
| **Rate-regime analysis** | Identifying distinct rate environments (e.g., low-rate era, rising-rate era, plateau) |
| **Historical percentile analysis** | Where does the current rate sit relative to the full historical distribution? |
| **Seasonality checks** | Testing for within-year patterns in rate movements |
| **Optional baseline forecasting** | Simple models (e.g., naive, drift, ETS) if appropriate — not the primary focus |

---

## Planned Business Questions

6. What are the historical trends and rate regimes in the 30-year fixed mortgage rate series?
7. How volatile have mortgage rates been in different eras, and where does the current environment fit?
8. Are there seasonal patterns in mortgage rate movements?

---

## What This Phase Is NOT

To keep expectations honest:

- This is **not** an advanced forecasting project (no ARIMA/GARCH production models promised)
- This is **not** a machine learning pipeline
- This is **not** a real-time prediction system
- Forecasting may be explored as a learning exercise, but the primary value is **descriptive and diagnostic analysis**

---

## Planned Tech Stack

- **Data ingestion**: `fredr` package for FRED API access
- **Time-series analysis**: `tseries`, `zoo`, `forecast` (for decomposition and diagnostics)
- **Visualization**: `ggplot2`
- **Reporting**: R Markdown or Quarto for reproducible analysis

---

## Planned Folder Structure

```
phase2_r/
├── README.md                  # This file
├── scripts/
│   ├── 01_data_ingestion.R    # FRED data download and preprocessing
│   ├── 02_eda.R               # Exploratory data analysis
│   ├── 03_stationarity.R      # Stationarity and unit root tests
│   ├── 04_rolling_stats.R     # Rolling averages and volatility
│   ├── 05_regime_analysis.R   # Rate-regime identification
│   ├── 06_percentiles.R       # Historical percentile context
│   └── 07_seasonality.R       # Seasonality decomposition and testing
├── output/
│   └── plots/                 # Visualization outputs
└── docs/
    ├── methods.md             # Analysis methods and interpretation
    └── limitations.md         # Scope and limitations
```

---

## Planned Deliverables

- [ ] R scripts for FRED data ingestion and preprocessing
- [ ] Exploratory data analysis: trend visualization, ACF/PACF plots
- [ ] Stationarity testing (ADF, KPSS)
- [ ] Rolling averages and rolling volatility calculations
- [ ] Rate-regime identification and visualization
- [ ] Historical percentile analysis for current rate context
- [ ] Seasonality decomposition
- [ ] Documentation: methods, interpretation, and limitations

---

## Academic & Certificate Context

This phase connects to two learning paths:

1. **Applied Time Series Analysis** — coursework at North Carolina Central University (NCCU)
2. **IBM Data Analytics with Excel and R Professional Certificate** — Coursera learning path covering R-based data analysis workflows

The goal is to apply time-series concepts from both programs to a real financial dataset, demonstrating practical analytical skills rather than theoretical complexity.

---

## Status Disclaimer

**This phase is planned but not yet implemented.** This folder is a placeholder to signal the multi-phase vision of the QuantPath Mortgage Rate Intelligence Stack. All content in this README is aspirational and subject to change based on coursework requirements, technical constraints, and portfolio priorities.

---

## References

- **FRED MORTGAGE30US**: https://fred.stlouisfed.org/series/MORTGAGE30US
- **fredr package**: https://github.com/sboysel/fredr
- **tseries package**: https://CRAN.R-project.org/package=tseries
- **R for Data Science**: https://r4ds.hadley.nz/
