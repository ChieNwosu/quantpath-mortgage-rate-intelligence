# QuantPath Mortgage Rate Intelligence Stack — Roadmap

This document outlines the multi-phase roadmap for the QuantPath Mortgage Rate Intelligence Stack, from the completed Phase 1 Excel workbook through planned future phases.

---

## Phase Status Overview

| Phase | Description | Status | Timeline |
|---|---|---|---|
| **Phase 1** | Excel financial modeling & dashboard | ✅ Complete | May 2026 |
| **Phase 2** | R time-series analysis layer | 🔲 Planned | Summer 2026 |
| **Phase 3** | SQL / cloud data layer | 🔲 Planned | Fall 2026 |
| **Phase 4** | Visualization & BI layer | 🔲 Planned | TBD |
| **Phase 5** | Cloud-native architecture | 🔲 Planned | TBD |

---

## Phase 1: Excel Financial Modeling & Dashboard ✅ Complete

**Timeline**: May 2026  
**Status**: ✅ Complete  
**Tech Stack**: Microsoft Excel, FRED data, Python/openpyxl, Kiro spec-driven development

### Deliverables

- [x] Excel workbook: `Rate_Shock_Loan_Affordability_Refinance_Dashboard.xlsx`
- [x] FRED/Freddie Mac MORTGAGE30US weekly series (Apr 1971 – May 2026, ~2,877 observations)
- [x] Central input panel for loan and refinance assumptions
- [x] Six-scenario rate shock analysis (−1.00% to +1.50%)
- [x] Full 360-payment amortization schedule with QA checks
- [x] Refinance break-even model with decision flag
- [x] Dashboard with five KPI cards and four chart placeholder zones
- [x] 29 workbook-scoped named ranges
- [x] Spec-driven development artifacts (requirements.md, design.md, tasks.md)
- [x] QA validation (32/32 checks passed)
- [x] Documentation (README, CHANGELOG, data dictionary, formula reference)
- [x] GitHub repository structure

### Business Questions Answered

1. How have 30-year fixed mortgage rates changed historically, and where does the latest rate sit relative to the past?
2. How much does a mortgage rate increase or decrease change the monthly payment on a representative loan?
3. How does the interest-rate environment affect total interest paid over the full life of the loan?
4. Given a fixed monthly payment budget, how much loan principal is affordable at different mortgage rates?
5. Under what conditions does refinancing become financially attractive, and how long does it take to break even?

### Limitations

- No macros or automation (manual FRED data refresh required)
- Manual chart insertion (openpyxl chart support unreliable)
- Single loan scenario (no multi-loan comparison)
- No tax or insurance (principal and interest only)
- No prepayment modeling

---

## Phase 2: R Time-Series Analysis Layer 🔲 Planned

**Timeline**: Summer 2026  
**Status**: 🔲 Planned  
**Tech Stack**: R, tidyverse, forecast, tsibble, fable, ggplot2

### Objectives

Extend the Excel workbook with advanced time-series analysis, connecting to Applied Time Series Analysis coursework at North Carolina Central University (NCCU).

### Planned Deliverables

- [ ] R scripts for FRED data ingestion and preprocessing
- [ ] Exploratory data analysis (EDA): trend, seasonality, autocorrelation
- [ ] ARIMA/ETS forecasting models for mortgage rate prediction
- [ ] Structural break detection (e.g., 2008 financial crisis, COVID-19, Fed rate hikes)
- [ ] Forecast accuracy metrics (RMSE, MAE, MAPE)
- [ ] Visualization: historical trends, forecast intervals, residual diagnostics
- [ ] Integration with Excel workbook: export forecasts to CSV for Excel import
- [ ] Documentation: R script guide, model selection rationale, forecast interpretation

### Business Questions to Answer

6. What are the historical trends and structural breaks in the 30-year fixed mortgage rate series?
7. Can we forecast mortgage rates 3–12 months ahead with reasonable accuracy?
8. How do forecast intervals inform refinance timing decisions?

### Tech Stack Details

- **Data ingestion**: `fredr` package for FRED API access
- **Time-series modeling**: `forecast`, `fable`, `tsibble`
- **Visualization**: `ggplot2`, `plotly`
- **Reporting**: R Markdown or Quarto for reproducible analysis

---

## Phase 3: SQL / Cloud Data Layer 🔲 Planned

**Timeline**: Fall 2026  
**Status**: 🔲 Planned  
**Tech Stack**: AWS S3, AWS Athena, SQL, Python (boto3)

### Objectives

Build a cloud-native data layer for scalable storage, querying, and integration with additional economic indicators.

### Planned Deliverables

- [ ] AWS S3 bucket for raw FRED data storage
- [ ] AWS Glue Data Catalog for schema management
- [ ] AWS Athena for SQL querying
- [ ] Python scripts for automated FRED data ingestion to S3
- [ ] SQL queries for historical rate analysis, aggregations, and joins
- [ ] Integration with additional FRED series (e.g., unemployment rate, GDP, inflation)
- [ ] Documentation: data pipeline architecture, SQL query library, AWS setup guide

### Business Questions to Answer

9. How do mortgage rates correlate with other macroeconomic indicators (unemployment, GDP, inflation)?
10. Can we identify leading indicators for mortgage rate movements?

### Tech Stack Details

- **Storage**: AWS S3 (raw CSV files)
- **Catalog**: AWS Glue Data Catalog (schema registry)
- **Query**: AWS Athena (serverless SQL)
- **Orchestration**: AWS Lambda or Step Functions for automated ingestion
- **IAM**: Least-privilege access policies for S3 and Athena

---

## Phase 4: Visualization & BI Layer 🔲 Planned

**Timeline**: TBD  
**Status**: 🔲 Planned  
**Tech Stack**: Tableau Public, Power BI, or Databricks SQL Analytics

### Objectives

Build interactive dashboards for public-facing portfolio presentation and stakeholder communication.

### Planned Deliverables

- [ ] Tableau Public dashboard with historical rate trends, forecast intervals, and refinance scenarios
- [ ] Interactive filters: date range, rate shock scenarios, loan assumptions
- [ ] Embedded dashboard in GitHub Pages project site
- [ ] Documentation: dashboard user guide, design rationale

### Business Questions to Answer

11. How can we present mortgage rate insights to non-technical stakeholders?
12. What interactive visualizations best support refinance decision-making?

---

## Phase 5: Cloud-Native Architecture 🔲 Planned

**Timeline**: TBD  
**Status**: 🔲 Planned  
**Tech Stack**: Databricks, Delta Lake, AWS (optional), Terraform

### Objectives

Build a production-grade, cloud-native data platform for scalable analytics and machine learning.

### Planned Deliverables

- [ ] Databricks lakehouse architecture with Delta Lake
- [ ] Automated ETL pipelines for FRED data ingestion
- [ ] Machine learning models for mortgage rate forecasting (MLflow)
- [ ] Infrastructure-as-code (Terraform) for reproducible deployment
- [ ] CI/CD pipeline for automated testing and deployment
- [ ] Documentation: architecture diagrams, deployment guide, cost analysis

### Business Questions to Answer

13. Can we build a production-grade forecasting system for mortgage rates?
14. How do we scale the analysis to include additional loan products (15-year, ARM, FHA)?

---

## Future Extensions (Exploratory)

- **Loan product comparison**: 15-year fixed, 5/1 ARM, FHA, VA loans
- **Prepayment modeling**: Extra principal payments, biweekly payment schedules
- **Tax and insurance**: Property tax, homeowners insurance, PMI
- **Multi-loan comparison**: Side-by-side scenario analysis
- **Real-time FRED data refresh**: Automated daily/weekly updates
- **Mobile-friendly dashboard**: Responsive design for mobile devices
- **API layer**: RESTful API for programmatic access to forecasts and scenarios

---

## Academic Context

This project is part of the **QuantPath** portfolio — a series of financial analytics projects connecting academic coursework, real-world datasets, and modern data tooling.

**Phase 2** will extend the analysis into R-based time-series modeling as part of **Applied Time Series Analysis** coursework at **North Carolina Central University (NCCU)**.

---

## Portfolio Framing

This project demonstrates:

- **Financial modeling**: Excel financial functions (PMT, PV, CUMIPMT, PPMT, IPMT)
- **Data engineering**: FRED data ingestion, preprocessing, validation
- **Spec-driven development**: Requirements → Design → Tasks workflow (Kiro)
- **Time-series analysis**: ARIMA/ETS forecasting, structural break detection (Phase 2)
- **Cloud data engineering**: AWS S3, Athena, Glue (Phase 3)
- **Visualization**: Tableau Public, interactive dashboards (Phase 4)
- **Cloud-native architecture**: Databricks, Delta Lake, Terraform (Phase 5)

---

## Honest Status Disclaimer

**Phase 1** is complete and production-ready.

**Phases 2–5** are planned but not yet implemented. This roadmap reflects the intended scope and technical direction, not completed work. The GitHub repository structure includes placeholder folders (`phase2_r/`, `phase3_sql/`) to signal the multi-phase vision, but these folders contain only README files outlining planned work.

This project is designed to grow incrementally, with each phase building on the previous one. The roadmap is subject to change based on coursework requirements, technical constraints, and portfolio priorities.

---

## Contact

**Author**: Chen Wosu  
**Institution**: North Carolina Central University (NCCU)  
**Program**: Applied Time Series Analysis  
**GitHub**: [chenwosu22](https://github.com/chenwosu22)  
**Project Repository**: [quantpath-mortgage-rate-intelligence](https://github.com/chenwosu22/quantpath-mortgage-rate-intelligence)
