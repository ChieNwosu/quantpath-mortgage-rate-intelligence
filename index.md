---
layout: default
title: Home
---

# QuantPath Mortgage Rate Intelligence Stack

**Phase 1 Deliverable:** Rate Shock Loan Affordability & Refinance Dashboard in Excel

---

## Overview

The **QuantPath Mortgage Rate Intelligence Stack** is a multi-phase financial analytics project that transforms mortgage rate data into actionable borrower insights. Phase 1 delivers a production-ready Excel workbook that models loan affordability, payment sensitivity, lifetime interest costs, amortization schedules, and refinance decision support.

This project connects applied Excel-based analytics, financial modeling, and R-focused learning from IBM's Data Analytics with Excel and R Professional Certificate (Coursera) with my broader QuantPath analytics portfolio.

> **Note:** The IBM certificate connection represents applied learning — not a completed IBM capstone project.

---

## Business Problem

Mortgage rates directly impact:
- **Home affordability** — How much house can a borrower afford at current rates?
- **Payment sensitivity** — How do rate changes affect monthly payments?
- **Refinance timing** — When does refinancing make financial sense?
- **Lifetime interest costs** — What is the total cost of borrowing over the loan term?

Borrowers, lenders, and financial advisors need a decision-support tool that:
1. Translates rate data into affordability metrics
2. Models payment scenarios across rate environments
3. Evaluates refinance opportunities with break-even analysis
4. Provides transparent, auditable calculations

---

## Dataset

**Source:** [FRED Economic Data](https://fred.stlouisfed.org/) / Freddie Mac  
**Series:** 30-Year Fixed Rate Mortgage Average in the United States  
**Series ID:** `MORTGAGE30US`  
**Frequency:** Weekly  
**Coverage:** Historical mortgage rates from 1971 to present

The dataset provides the historical and current rate environment. Borrower-specific assumptions (loan amount, down payment, refinance scenarios) are configurable model inputs.

---

## What the Workbook Does

The Excel workbook is a **reusable financial modeling template** that:

1. **Ingests mortgage rate data** from the FRED/Freddie Mac series
2. **Models payment sensitivity** across rate scenarios (current, historical, stress-test)
3. **Calculates loan affordability** based on a target monthly payment budget
4. **Generates full amortization schedules** with principal/interest breakdowns
5. **Evaluates refinance opportunities** with break-even analysis and decision flags
6. **Presents results in a dashboard** with KPI cards and scenario comparisons

---

## Workbook Assumptions (Configurable Inputs)

The **Assumptions** sheet accepts the following user-configurable inputs:

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

> **Scope note:** The workbook does *not* model income, monthly debt, DTI ratio, credit score, taxes, insurance, PMI, or ARM loans. It focuses on principal-and-interest scenarios using fixed-rate assumptions.

---

## Business Questions Answered

### 1. Payment Sensitivity & Rate Shock
- How much does a 1% rate increase affect monthly payments?
- What is the payment difference between current rates and historical averages?
- How do rate shocks impact long-term affordability?

### 2. Loan Affordability
- Given a fixed monthly payment budget, how much loan principal is affordable at different mortgage rates?
- How does affordability change across different rate environments?

### 3. Refinance Decision Support
- Should the borrower refinance at current rates?
- What are the monthly savings from refinancing?
- What is the break-even period for refinance closing costs?
- How much lifetime interest can be saved?

### 4. Lifetime Interest Analysis
- What is the total interest paid over the loan term?
- How does the interest burden change with different rates?
- What is the principal-to-interest ratio over time?

---

## Workbook Structure

The workbook is organized into **7 interconnected worksheets**:

| Worksheet | Purpose |
|-----------|---------|
| **Dashboard** | Executive summary with KPI cards and scenario comparisons |
| **Assumptions** | Centralized input hub for loan parameters and refinance scenario |
| **Rate_Data** | FRED/Freddie Mac mortgage rate time series |
| **Rate_Shock_Model** | Payment calculations across current, historical, and stress-test rates |
| **Amortization_Schedule** | Full loan amortization with principal/interest breakdown |
| **Refinance_Analysis** | Refinance evaluation with break-even and savings analysis |
| **Project_Map** | Workbook health check and navigation guide |

---

## Key Excel Formulas Used

### Monthly Payment Calculation
```excel
=PMT(rate/12, term*12, -loan_amount)
```
Calculates the monthly payment for a given rate, term, and loan amount.

### Maximum Affordable Loan (from budget)
```excel
=PV(rate/12, term*12, -target_monthly_payment)
```
Reverse-engineers the maximum loan principal from the target monthly payment.

### Amortization Schedule
```excel
Interest Payment: =IPMT(rate/12, period, term*12, -principal)
Principal Payment: =PPMT(rate/12, period, term*12, -principal)
Remaining Balance: =previous_balance - principal_payment
```

### Refinance Break-Even Period
```excel
=refinance_closing_costs / monthly_savings
```
Calculates how many months it takes for monthly savings to offset closing costs.

### Refinance Decision Logic
```excel
=IF(AND(monthly_savings > 0, breakeven_months < holding_period_months),
    "Refinance Recommended",
    "Stay with Current Loan")
```

---

## How to Use the Workbook

### Step 1: Update Mortgage Rate Data
1. Download the latest `MORTGAGE30US` series from [FRED](https://fred.stlouisfed.org/series/MORTGAGE30US)
2. Paste the data into the `Rate_Data` worksheet
3. The workbook automatically pulls the most recent rate into the `Assumptions` sheet

### Step 2: Configure Assumptions
Navigate to the `Assumptions` worksheet and update:
- **Loan parameters:** Home price, down payment percentage, target monthly budget
- **Refinance scenario:** Remaining balance, current rate, proposed rate, closing costs, holding period

### Step 3: Review the Dashboard
The `Dashboard` worksheet displays:
- **Rate Shock KPIs:** Payment differences across rate scenarios
- **Affordability:** Maximum affordable loan at different rates
- **Refinance Decision:** Monthly savings, break-even period, recommendation

### Step 4: Drill into Details
- **Rate_Shock_Model:** Compare payments across current, historical, and stress-test rates
- **Amortization_Schedule:** View the full payment schedule with principal/interest breakdown
- **Refinance_Analysis:** Evaluate refinance scenarios with detailed savings calculations

---

## Workbook Screenshots

### Project Map
![Project Map](screenshots/workbook/Project_Map.png)

### Assumptions (Input Panel)
![Assumptions](screenshots/workbook/Assumptions.png)

### Rate Shock Model
![Rate Shock Model](screenshots/workbook/Rate_Shock_Model.png)

### Amortization Schedule QA
![Amortization Schedule](screenshots/workbook/Amortization_QA.png)

### Refinance Analysis
![Refinance Analysis](screenshots/workbook/Refinance_Analysis.png)

---

## Limitations

### Phase 1 Scope
- **Static rate data:** Requires manual updates from FRED (no live API integration)
- **Single-borrower model:** Does not support co-borrower scenarios
- **Fixed-rate loans only:** Does not model adjustable-rate mortgages (ARMs)
- **No tax/insurance:** Does not include property taxes, homeowners insurance, or PMI
- **No income/DTI modeling:** Does not model borrower income or debt-to-income ratios
- **No prepayment modeling:** Does not calculate the impact of extra principal payments

### Data Constraints
- **Weekly frequency:** Rate data is weekly; daily rate changes are not captured
- **National average:** Rates are U.S. national averages; regional variations are not modeled
- **Historical data only:** No forward-looking rate forecasts (planned for Phase 2)

---

## Phase 2 Roadmap: R-Based Time-Series Analysis

Phase 2 will extend the Excel foundation with an **R-based time-series analysis layer** connecting to Applied Time Series Analysis coursework at NCCU and IBM's Data Analytics with Excel and R Professional Certificate learning path.

### Planned Focus Areas
- Stationarity testing (ADF, KPSS)
- Rolling averages and rolling volatility
- Rate-regime analysis
- Historical percentile analysis
- Seasonality checks
- Optional baseline forecasting

### Deliverables (Planned)
- R scripts for data ingestion and exploratory analysis
- Time-series diagnostics and visualizations
- Documentation of methods and interpretation

**Status:** Planned (not yet implemented)

---

## Future Extensions

- **Phase 3: SQL / Cloud Data Layer** — Centralized data storage and querying (planned)
- **Databricks, Tableau, AWS** — Future extensions only (not yet scoped)

---

## Technical Stack

### Phase 1 (Complete)
- **Excel:** Financial modeling, dashboard design, formula-based calculations
- **Data Source:** FRED/Freddie Mac `MORTGAGE30US` series
- **Python / openpyxl:** Workbook build automation (spec-driven development)
- **Kiro:** Spec-driven development workflow
- **Version Control:** Git/GitHub

### Phase 2 (Planned)
- **R:** Time-series analysis, rolling statistics, regime analysis
- **R Packages:** `tidyverse`, `tseries`, `ggplot2`, `fredr`

---

## About the Author

**Chiemela Joseph Nwosu**  
Undergraduate Information Technology student at North Carolina Central University, concentrating in Data Analytics.

This project demonstrates:
- Financial modeling and dashboard design in Excel
- Data-driven decision support for mortgage affordability and refinancing
- Structured project planning with phased roadmap execution
- Portfolio-ready documentation and version control practices

**GitHub:** [ChieNwosu](https://github.com/ChieNwosu)  
**LinkedIn:** [Chiemela Joseph Nwosu](https://www.linkedin.com/in/chiemela-nwosu-23a791144/)

---

## License

This project is licensed under the MIT License. See `LICENSE` for details.

---

## Acknowledgments

- **Data Source:** Federal Reserve Economic Data (FRED) / Freddie Mac
- **Coursework:** Applied Time Series Analysis, NCCU
- **Certificate:** IBM Data Analytics with Excel and R Professional Certificate (Coursera)
- **Tools:** Microsoft Excel, R, Git/GitHub, Jekyll (GitHub Pages)

---

## Contact

For questions, feedback, or collaboration opportunities:
- **Email:** JosephCNwosu@yahoo.com
- **GitHub Issues:** [Open an issue](https://github.com/ChieNwosu/quantpath-mortgage-rate-intelligence/issues)

---

**Last Updated:** May 26, 2026
