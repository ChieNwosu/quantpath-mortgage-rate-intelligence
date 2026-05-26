# Changelog

All notable changes to this project are documented here.

---

## [1.0.0] — 2026-05-19

### Phase 1 — Excel Financial Modeling & Dashboard (Complete)

**Workbook**: `Rate_Shock_Loan_Affordability_Refinance_Dashboard.xlsx`

#### Added
- **Rate_Data** sheet: imported FRED/Freddie Mac MORTGAGE30US weekly series (Apr 1971 – May 2026, ~2,877 observations); helper columns for Rate_Decimal, Year, Month_Year, and CurrentRateLine; summary statistics block (latest rate, historical min/max/avg, 52-week trailing average)
- **Assumptions** sheet: central input panel for loan inputs (home price, down payment, loan amount, term, base rate, monthly budget) and refinance inputs (remaining balance, current/proposed rates, remaining term, closing costs, holding period); data validation on all user-editable cells
- **Rate_Shock_Model** sheet: six-scenario rate shock analysis (−1.00% to +1.50%) computing monthly payment (PMT), payment delta vs. base, total lifetime interest (CUMIPMT), total loan cost, and affordable principal (PV)
- **Amortization_Schedule** sheet: full 360-payment base-case amortization schedule using PPMT/IPMT; summary block with PASS/FAIL formula-consistency checks
- **Refinance_Analysis** sheet: break-even model computing current vs. new payment, monthly savings, break-even months, and a conditional decision flag ("Refinance Makes Sense" / "Does Not Break Even in Holding Period")
- **Dashboard** sheet: five KPI cards (latest rate, base case payment, total lifetime interest, affordable loan amount, refinance break-even); chart placeholder zones for four charts (to be inserted manually in Excel); refinance summary table
- **Project_Map** sheet: executive overview with project objective, business questions table, workbook navigation map, formula family summary, scope notes, portfolio framing statement, data source attribution, and workbook health check formula
- 29 workbook-scoped named ranges covering all cross-sheet references
- Spec-driven development artifacts: requirements.md, design.md, tasks.md (via Kiro)

#### Technical notes
- All PMT/PPMT/IPMT formulas use the standard cash-flow sign convention: negative present value argument, no leading minus on the function itself
- CUMIPMT and PV formulas use a leading minus to convert their negative return values to positive display values
- No macros or VBA; workbook operates in standard Excel desktop without add-ins
