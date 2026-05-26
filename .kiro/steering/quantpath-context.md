# QuantPath Context — Mortgage Rate Intelligence Stack

## Core Identity

QuantPath™ is a personal applied learning and portfolio system for building technical depth at the intersection of:

- Financial analytics
- Data analytics
- Excel financial modeling
- R / statistics / time-series analysis
- SQL
- Cloud / data engineering
- AI-assisted documentation
- Decision-support systems
- Project / product management
- Responsible AI / explainable analytics

QuantPath is not a generic finance brand or trading signal system. It is a structured learning and project-building path that turns coursework, certifications, technical books, and real-world datasets into portfolio-ready analytical systems.

Long-term direction:

> AI/Data + Risk-Aware Intelligent Systems Architecture — with a finance/quant analytics emphasis.

---

## Current Flagship Project

**Project:** QuantPath Mortgage Rate Intelligence Stack  
**Repo:** https://github.com/ChieNwosu/quantpath-mortgage-rate-intelligence  
**Phase 1 Deliverable:** Rate Shock Loan Affordability & Refinance Dashboard (Excel)

The workbook uses the FRED / Freddie Mac `MORTGAGE30US` 30-Year Fixed Mortgage Rate series to translate mortgage-rate data into practical household finance scenarios. It is a reusable decision-support template — not just a calculator.

---

## Phase Status

| Phase | Description | Status |
|-------|-------------|--------|
| Phase 1 | Excel financial modeling & dashboard | ✅ **Complete** |
| Phase 2 | R time-series analysis layer | 🔲 Planned |
| Phase 3 | SQL / data layer | 🔲 Future |
| Future | Databricks, Tableau, Streamlit, GitHub Pages, AWS, agents | 🔲 Future extensions only |

---

## Phase 1 — Completed

The Phase 1 Excel workbook is finalized. Sheets:

1. `Project_Map`
2. `Rate_Data`
3. `Assumptions`
4. `Rate_Shock_Model`
5. `Amortization_Schedule`
6. `Refinance_Analysis`
7. `Dashboard`

Business questions answered:

1. Historical rate context and where the latest rate sits
2. Monthly payment sensitivity to rate changes
3. Lifetime interest cost across rate scenarios
4. Affordable loan amount given a fixed monthly budget
5. Refinance break-even timing and decision support

Key Excel functions: `PMT`, `PV`, `CUMIPMT`, `PPMT`, `IPMT`, `IF`, `IFERROR`  
Features: named ranges, conditional formatting, dashboard charts, structured tables

**Important caveat:** The workbook does NOT model income, DTI, credit score, taxes, insurance, PMI, ARM loans, HOA fees, or regional housing prices.

---

## Phase 2 — R Time-Series Layer (Planned)

Purpose: Study the behavior of the mortgage-rate series itself.

Focus areas:
- Data loading and cleaning in R
- Time-series visualization
- Rolling averages and rolling volatility
- Stationarity testing
- Seasonality checks
- Rate-regime analysis
- Historical percentile analysis
- Affordability implications by rate regime
- Optional baseline forecasting (exploratory only)

Course-relevant R packages:
- `TSA` — ARIMA-style modeling, ACF/PACF, residual diagnostics
- `tseries` — stationarity / unit-root testing
- `uroot` — seasonal unit-root testing
- `locfit` — nonlinear smoothing and long-run trend visualization
- `leaps` — variable selection for engineered affordability features
- `MTS` — future multivariate macro-financial extension

Phase 2 must NOT overclaim prediction accuracy. Forecasting, if included, is exploratory or baseline — not a reliable mortgage-rate prediction system.

---

## Phase 3 — SQL / Data Layer (Future)

A reproducible finance analytics learning layer with SQL business questions from beginner to advanced. May start with DuckDB or Databricks Free Edition; Athena-compatible SQL as a future AWS-aligned extension.

---

## Applied Learning Context

- Enrolled in IBM's **Data Analytics with Excel and R Professional Certificate** (Coursera)
- Taking **Applied Time Series Analysis** at North Carolina Central University (NCCU)
- Frame as applied learning, NOT an official IBM capstone unless explicitly stated

Preferred wording:

> "This project connects applied Excel-based analytics, financial modeling, and R-focused learning from IBM's Data Analytics with Excel and R Professional Certificate with my broader QuantPath analytics portfolio."

---

## Author

**Chiemela Joseph Nwosu**  
Undergraduate Information Technology student at North Carolina Central University, concentrating in Data Analytics.

**GitHub:** [ChieNwosu](https://github.com/ChieNwosu)  
**LinkedIn:** [Chiemela Joseph Nwosu](https://www.linkedin.com/in/chiemela-nwosu-23a791144/)
