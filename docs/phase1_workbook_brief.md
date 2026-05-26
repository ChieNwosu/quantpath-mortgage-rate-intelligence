# Phase 1 Workbook Brief

**Project**: QuantPath Mortgage Rate Intelligence Stack  
**Phase**: Phase 1 — Excel Financial Modeling & Dashboard  
**Status**: ✅ Complete  
**Workbook**: `Rate_Shock_Loan_Affordability_Refinance_Dashboard.xlsx`

---

## Business Problem

Mortgage rates are a critical driver of household affordability and long-term financial outcomes. A 1% rate increase can add hundreds of dollars to monthly payments and tens of thousands of dollars in lifetime interest. Yet most borrowers lack tools to quickly model rate sensitivity, compare scenarios, or evaluate refinance opportunities.

This workbook translates the FRED/Freddie Mac 30-year fixed mortgage rate series into actionable household finance insights, answering five core business questions about affordability, payment sensitivity, lifetime interest burden, and refinance decision-making.

---

## Business Questions

| # | Question | Answer Location |
|---|---|---|
| **BQ1** | How have 30-year fixed mortgage rates changed historically, and where does the latest rate sit relative to the past? | Rate_Data sheet; Dashboard |
| **BQ2** | How much does a mortgage rate increase or decrease change the monthly payment on a representative loan? | Rate_Shock_Model; Dashboard |
| **BQ3** | How does the interest-rate environment affect total interest paid over the full life of the loan? | Rate_Shock_Model; Amortization_Schedule; Dashboard |
| **BQ4** | Given a fixed monthly payment budget, how much loan principal is affordable at different mortgage rates? | Rate_Shock_Model; Dashboard |
| **BQ5** | Under what conditions does refinancing become financially attractive, and how long does it take to break even? | Refinance_Analysis; Dashboard |

---

## Scope

### In Scope (Phase 1)

- Import and prepare FRED/Freddie Mac MORTGAGE30US weekly series (Apr 1971 – May 2026, ~2,877 observations)
- Central input panel for loan and refinance assumptions
- Six-scenario rate shock analysis (−1.00% to +1.50%)
- Full 360-payment amortization schedule with QA checks
- Refinance break-even model with decision flag
- Dashboard with five KPI cards and four chart placeholder zones
- 29 workbook-scoped named ranges
- Spec-driven development artifacts (requirements.md, design.md, tasks.md)

### Out of Scope (Phase 1)

- Property taxes, homeowners insurance, PMI
- Prepayment modeling
- Multiple loan comparison
- Automated FRED data refresh
- Macros or VBA
- Advanced time-series analysis (deferred to Phase 2)
- Cloud data layer (deferred to Phase 3)

---

## Formula Families

The workbook uses four core Excel financial function families:

### 1. PMT (Payment)

Computes the monthly payment for a fixed-rate loan.

**Syntax**: `PMT(rate, nper, pv, [fv], [type])`

**Usage in Workbook**:
```excel
=PMT(Assump_BaseRate/12, Assump_LoanTerm, -Assump_LoanAmt)
```

**Sign Convention**: Use a negative present value argument. The function returns a positive payment directly. **Do not add a leading minus sign to the function itself.**

---

### 2. PPMT / IPMT (Principal and Interest Components)

Computes the principal and interest portions of a specific payment.

**Syntax**: 
- `PPMT(rate, per, nper, pv, [fv], [type])`
- `IPMT(rate, per, nper, pv, [fv], [type])`

**Usage in Workbook** (Amortization_Schedule):
```excel
=PPMT(Assump_BaseRate/12, A13, Assump_LoanTerm, -Assump_LoanAmt)
=IPMT(Assump_BaseRate/12, A13, Assump_LoanTerm, -Assump_LoanAmt)
```

**Sign Convention**: Same as PMT — use a negative present value argument, no leading minus on the function.

---

### 3. CUMIPMT (Cumulative Interest)

Computes the total interest paid over a range of payments.

**Syntax**: `CUMIPMT(rate, nper, pv, start_period, end_period, type)`

**Usage in Workbook** (Rate_Shock_Model):
```excel
=-CUMIPMT(Assump_BaseRate/12, Assump_LoanTerm, Assump_LoanAmt, 1, Assump_LoanTerm, 0)
```

**Sign Convention**: CUMIPMT returns a negative value (interest paid out). Use a leading minus to convert to positive for display.

---

### 4. PV (Present Value / Affordable Principal)

Computes the loan amount affordable given a fixed monthly payment budget.

**Syntax**: `PV(rate, nper, pmt, [fv], [type])`

**Usage in Workbook** (Rate_Shock_Model):
```excel
=-PV(Assump_BaseRate/12, Assump_LoanTerm, Assump_MonthlyBudget)
```

**Sign Convention**: PV returns a negative value (loan amount). Use a leading minus to convert to positive for display.

---

## Data Source

**Series**: MORTGAGE30US — 30-Year Fixed Rate Mortgage Average in the United States  
**Provider**: Federal Reserve Bank of St. Louis (FRED) / Freddie Mac Primary Mortgage Market Survey  
**Frequency**: Weekly (Thursday)  
**Coverage**: April 2, 1971 – May 14, 2026 (~2,877 observations)  
**Units**: Percent, not seasonally adjusted  
**URL**: https://fred.stlouisfed.org/series/MORTGAGE30US

---

## Tech Stack

- **Microsoft Excel** — financial modeling, PMT/PV/CUMIPMT/PPMT formula families, named ranges, conditional formatting
- **FRED / Freddie Mac** — primary mortgage rate data source
- **Python / openpyxl** — workbook build automation (spec-driven development)
- **Kiro** — spec-driven development workflow (requirements → design → tasks)

---

## Deliverables

| Deliverable | Status |
|---|---|
| Excel workbook | ✅ Complete |
| Spec artifacts (requirements.md, design.md, tasks.md) | ✅ Complete |
| QA validation (32/32 checks passed) | ✅ Complete |
| Documentation (README, CHANGELOG, data dictionary, formula reference) | ✅ Complete |
| GitHub repository structure | ✅ Complete |
| GitHub Pages project site | 🔲 Planned (optional, may be added later) |

---

## Limitations

- **No macros or automation**: The workbook does not auto-refresh the FRED data. To update the dataset, download the latest CSV from FRED and replace the data in the **Rate_Data** sheet.
- **Manual chart insertion**: The four Dashboard charts must be inserted manually in Excel. Openpyxl chart support is unreliable for complex series.
- **Single loan scenario**: The workbook models one loan at a time. To compare multiple loans, duplicate the workbook or adjust inputs and record outputs separately.
- **No tax or insurance**: The payment calculations include principal and interest only. Property taxes, homeowners insurance, and PMI are not included.
- **No prepayment modeling**: The amortization schedule assumes no extra principal payments.

---

## Next Steps

- **Phase 2 (R)**: Time-series analysis — stationarity testing, rolling statistics, rate-regime analysis, seasonality checks; connects to Applied Time Series Analysis coursework at NCCU and IBM's Data Analytics with Excel and R Professional Certificate
- **Phase 3 (SQL)**: Structured data layer for querying and joining with additional economic indicators (planned, platform TBD)

See [`roadmap.md`](roadmap.md) for the full multi-phase plan.
