# QuantPath Mortgage Rate Intelligence Stack — Roadmap

This document outlines the multi-phase roadmap for the QuantPath Mortgage Rate Intelligence Stack, from the completed Phase 1 Excel workbook through planned future phases.

---

## Phase Status Overview

| Phase | Description | Status | Timeline |
|---|---|---|---|
| **Phase 1** | Excel financial modeling & dashboard | ✅ Complete | May 2026 |
| **Phase 2** | R time-series analysis layer | 🔲 Planned | Summer 2026 |
| **Phase 3** | SQL / cloud data layer | 🔲 Planned | TBD |

**Future extensions** (Databricks, Tableau, AWS, agent/chatbot features) are exploratory ideas only — not scoped or scheduled.

---

## Phase 1: Excel Financial Modeling & Dashboard ✅ Complete

**Timeline**: May 2026  
**Status**: ✅ Complete  
**Tech Stack**: Microsoft Excel, FRED data, Python/openpyxl, Kiro spec-driven development

### Deliverables

- [x] Excel workbook: `Rate_Shock_Loan_Affordability_Refinance_Dashboard.xlsx`
- [x] FRED/Freddie Mac MORTGAGE30US weekly series (Apr 1971 - May 2026, ~2,877 observations)
- [x] Central input panel for loan and refinance assumptions
- [x] Six-scenario rate shock analysis (-1.00% to +1.50%)
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

### Workbook Assumptions (Configurable Inputs)

- Home price
- Down payment percentage
- Loan amount (derived)
- Latest / base mortgage rate (from FRED data)
- Target monthly payment budget
- Remaining refinance balance
- Current mortgage rate
- Proposed refinance rate
- Closing costs
- Expected holding period

> **Scope note:** The workbook does *not* model income, monthly debt, DTI ratio, credit score, taxes, insurance, PMI, or ARM loans.

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
**Tech Stack**: R, tidyverse, tseries, ggplot2, fredr

### Overview

Phase 2 will add an R-based time-series analysis layer focused on understanding the historical behavior of the MORTGAGE30US series. This connects to Applied Time Series Analysis coursework at North Carolina Central University (NCCU) and IBM's Data Analytics with Excel and R Professional Certificate learning path.

### Planned Focus Areas

- **Stationarity testing** — ADF, KPSS tests to assess series properties
- **Rolling averages** — Moving averages to identify trend direction
- **Rolling volatility** — Windowed standard deviation to measure rate stability
- **Rate-regime analysis** — Identifying distinct rate environments (e.g., low-rate era, rising-rate era)
- **Historical percentile analysis** — Where does the current rate sit relative to historical distribution?
- **Seasonality checks** — Testing for within-year patterns in rate movements
- **Optional baseline forecasting** — Simple models if appropriate; not the primary focus

### Planned Deliverables

- [ ] R scripts for FRED data ingestion and preprocessing
- [ ] Exploratory data analysis (EDA): trend, autocorrelation, stationarity
- [ ] Rolling statistics and volatility analysis
- [ ] Rate-regime identification and visualization
- [ ] Historical percentile context for current rates
- [ ] Seasonality decomposition
- [ ] Documentation: methods, interpretation, and limitations

### Business Questions to Explore

6. What are the historical trends and rate regimes in the 30-year fixed mortgage rate series?
7. How volatile have mortgage rates been in different eras, and where does the current environment fit?
8. Are there seasonal patterns in mortgage rate movements?

### Tech Stack Details

- **Data ingestion**: `fredr` package for FRED API access
- **Time-series analysis**: `tseries`, `zoo`, `forecast` (for decomposition)
- **Visualization**: `ggplot2`
- **Reporting**: R Markdown or Quarto for reproducible analysis

---

## Phase 3: SQL / Cloud Data Layer 🔲 Planned

**Timeline**: TBD  
**Status**: 🔲 Planned  
**Tech Stack**: SQL (specific platform TBD)

### Overview

Phase 3 will build a structured data layer for querying and joining the mortgage rate series with additional context. Specific cloud platform decisions have not been made.

### Planned Objectives

- Centralized storage for FRED rate data
- SQL queries for historical rate analysis and aggregations
- Potential integration with additional economic indicators
- Documentation: data pipeline architecture and query library

---

## Future Extensions (Exploratory Only)

The following are ideas for future development. They are **not scoped, not scheduled, and not committed**:

- **Visualization / BI layer** — Tableau Public or similar interactive dashboards
- **Cloud-native architecture** — Databricks, Delta Lake, AWS services
- **Additional loan products** — 15-year fixed, ARM comparison
- **Prepayment modeling** — Extra principal payments, biweekly schedules
- **Tax and insurance** — Property tax, homeowners insurance, PMI layers
- **Real-time data refresh** — Automated FRED data ingestion
- **Agent / chatbot features** — Conversational access to rate insights

---

## Academic & Certificate Context

This project is part of the **QuantPath** portfolio — a series of financial analytics projects connecting academic coursework, professional certificate learning, real-world datasets, and modern data tooling.

- **Phase 1** connects to applied Excel-based analytics and financial modeling.
- **Phase 2** connects to Applied Time Series Analysis coursework at NCCU and IBM's Data Analytics with Excel and R Professional Certificate (Coursera).

> The IBM certificate connection represents applied learning — not a completed IBM capstone project.

---

## Honest Status Disclaimer

**Phase 1** is complete and production-ready.

**Phases 2-3** are planned but not yet implemented. This roadmap reflects the intended scope and technical direction, not completed work. The GitHub repository structure includes placeholder folders (`phase2_r/`, `phase3_sql/`) to signal the multi-phase vision, but these folders contain only README files outlining planned work.

**Future extensions** are exploratory ideas only. They are not scheduled, scoped, or guaranteed.

This project is designed to grow incrementally, with each phase building on the previous one. The roadmap is subject to change based on coursework requirements, technical constraints, and portfolio priorities.

---

## Contact

**Author**: Chiemela Joseph Nwosu  
**Institution**: North Carolina Central University (NCCU)  
**Program**: Information Technology, concentration in Data Analytics  
**GitHub**: [ChieNwosu](https://github.com/ChieNwosu)  
**Project Repository**: [quantpath-mortgage-rate-intelligence](https://github.com/ChieNwosu/quantpath-mortgage-rate-intelligence)
