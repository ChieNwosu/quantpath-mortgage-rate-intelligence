# Data Dictionary

This document defines all columns, named ranges, and key cell references used in the Phase 1 Excel workbook.

---

## Dataset Columns (Rate_Data Sheet)

| Column | Type | Description | Source |
|---|---|---|---|
| `DATE` | Date (YYYY-MM-DD) | Observation date (weekly Thursday) | FRED MORTGAGE30US |
| `MORTGAGE30US` | Numeric (percent) | 30-year fixed mortgage rate | FRED MORTGAGE30US |
| `Rate_Decimal` | Numeric (decimal) | Mortgage rate converted to decimal for Excel formulas (e.g., 6.36% → 0.0636) | Calculated |
| `Year` | Integer | Year extracted from DATE | Calculated |
| `Month_Year` | Text | Date formatted as "MMM YYYY" for chart labels | Calculated |
| `CurrentRateLine` | Numeric | Marks the latest rate for chart annotation (latest row = rate value, all others = blank) | Calculated |

---

## Named Ranges (Workbook-Scoped)

All named ranges are workbook-scoped and reference cells in the **Assumptions** sheet unless otherwise noted.

### Loan Input Named Ranges

| Named Range | Cell Reference | Description |
|---|---|---|
| `Assump_HomePrice` | Assumptions!B4 | Home purchase price |
| `Assump_DownPct` | Assumptions!B5 | Down payment percentage (e.g., 0.20 = 20%) |
| `Assump_LoanAmt` | Assumptions!B6 | Loan amount (calculated: HomePrice × (1 − DownPct)) |
| `Assump_LoanTerm` | Assumptions!B7 | Loan term in months (typically 360 for 30-year) |
| `Assump_BaseRate` | Assumptions!B8 | Base mortgage rate (from Rate_Data latest rate) |
| `Assump_MonthlyBudget` | Assumptions!B9 | Target monthly payment budget |

### Refinance Input Named Ranges

| Named Range | Cell Reference | Description |
|---|---|---|
| `Assump_RemBal` | Assumptions!B12 | Remaining loan balance before refinance |
| `Assump_CurrentRate` | Assumptions!B13 | Current mortgage rate (before refinance) |
| `Assump_ProposedRate` | Assumptions!B14 | Proposed new mortgage rate (after refinance) |
| `Assump_RemTerm` | Assumptions!B15 | Remaining loan term in months |
| `Assump_ClosingCosts` | Assumptions!B16 | Refinance closing costs |
| `Assump_HoldingPeriod` | Assumptions!B18 | Expected holding period in months |

### Rate_Shock_Model Named Ranges

| Named Range | Cell Reference | Description |
|---|---|---|
| `RateShock_BasePayment` | Rate_Shock_Model!D13 | Monthly payment for base case scenario |
| `RateShock_BaseTotalInterest` | Rate_Shock_Model!F13 | Total lifetime interest for base case scenario |
| `RateShock_BaseAffordable` | Rate_Shock_Model!H13 | Affordable principal for base case scenario (given target monthly budget) |

### Amortization_Schedule Named Ranges

| Named Range | Cell Reference | Description |
|---|---|---|
| `Amort_TotalPrincipal` | Amortization_Schedule!B3 | Total principal paid over 360 payments (should equal loan amount) |
| `Amort_TotalInterest` | Amortization_Schedule!B4 | Total interest paid over 360 payments |
| `Amort_TotalPayments` | Amortization_Schedule!B5 | Total payments (principal + interest) |
| `Amort_QA_PrincipalCheck` | Amortization_Schedule!I6 | QA check: total principal = loan amount |
| `Amort_QA_PaymentCheck` | Amortization_Schedule!I8 | QA check: total payments = sum of all payment column values |

### Refinance_Analysis Named Ranges

| Named Range | Cell Reference | Description |
|---|---|---|
| `Refi_CurrentPayment` | Refinance_Analysis!B13 | Current monthly payment (before refinance) |
| `Refi_ProposedPayment` | Refinance_Analysis!B14 | Proposed new monthly payment (after refinance) |
| `Refi_MonthlySavings` | Refinance_Analysis!B15 | Monthly savings (current − proposed) |
| `Refi_BreakEvenMonths` | Refinance_Analysis!B16 | Break-even months (closing costs ÷ monthly savings) |
| `Refi_Decision` | Refinance_Analysis!B22 | Decision flag: "Refinance Makes Sense" or "Does Not Break Even in Holding Period" |

### Rate_Data Named Ranges

| Named Range | Cell Reference | Description |
|---|---|---|
| `RateData_LatestRate` | Rate_Data!B3 | Latest mortgage rate from FRED series |
| `RateData_MinRate` | Rate_Data!B4 | Historical minimum rate |
| `RateData_MaxRate` | Rate_Data!B5 | Historical maximum rate |
| `RateData_AvgRate` | Rate_Data!B6 | Historical average rate |
| `RateData_52WkAvg` | Rate_Data!B8 | 52-week trailing average rate |

---

## Key Cell References (Not Named Ranges)

### Project_Map

| Cell | Description |
|---|---|
| `F2` | Workbook health check formula (checks for errors in key model cells) |

### Dashboard

| Cell | Description |
|---|---|
| `B3` | KPI Card 1: Latest Mortgage Rate |
| `D3` | KPI Card 2: Base Case Monthly Payment |
| `F3` | KPI Card 3: Total Lifetime Interest |
| `B8` | KPI Card 4: Affordable Loan Amount |
| `D8` | KPI Card 5: Refinance Break-Even Months |
| `B13:E17` | Refinance Summary Table |

---

## Data Types and Validation

### Assumptions Sheet Input Cells

All yellow-highlighted input cells in the **Assumptions** sheet have data validation rules:

| Cell | Validation Rule |
|---|---|
| B4 (Home Price) | Decimal ≥ 0 |
| B5 (Down Payment %) | Decimal between 0 and 1 |
| B7 (Loan Term) | Whole number ≥ 1 |
| B9 (Monthly Budget) | Decimal ≥ 0 |
| B12 (Remaining Balance) | Decimal ≥ 0 |
| B13 (Current Rate) | Decimal between 0 and 1 |
| B14 (Proposed Rate) | Decimal between 0 and 1 |
| B15 (Remaining Term) | Whole number ≥ 1 |
| B16 (Closing Costs) | Decimal ≥ 0 |
| B18 (Holding Period) | Whole number ≥ 1 |

---

## Formula Patterns

### Rate Conversion

FRED rates are in percent (e.g., 6.36). Excel financial functions require decimal (e.g., 0.0636).

**Conversion formula** (Rate_Data!D column):
```excel
=C13/100
```

### Monthly Rate

All financial functions use monthly rate, not annual.

**Monthly rate formula**:
```excel
=Assump_BaseRate/12
```

### Payment Calculation

**PMT formula** (Rate_Shock_Model!D13):
```excel
=IFERROR(PMT(C13/12, Assump_LoanTerm, -Assump_LoanAmt), "—")
```

**Sign convention**: Use `-Assump_LoanAmt` (negative present value). The function returns a positive payment directly. **Do not add a leading minus sign to the function itself.**

### Cumulative Interest Calculation

**CUMIPMT formula** (Rate_Shock_Model!F13):
```excel
=IFERROR(-CUMIPMT(C13/12, Assump_LoanTerm, Assump_LoanAmt, 1, Assump_LoanTerm, 0), "—")
```

**Sign convention**: CUMIPMT returns a negative value (interest paid out). Use a leading minus to convert to positive for display.

### Affordable Principal Calculation

**PV formula** (Rate_Shock_Model!H13):
```excel
=IFERROR(-PV(C13/12, Assump_LoanTerm, Assump_MonthlyBudget), "—")
```

**Sign convention**: PV returns a negative value (loan amount). Use a leading minus to convert to positive for display.

---

## QA Check Formulas

### Amortization_Schedule QA Checks

**Principal Check** (I6):
```excel
=IF(ABS(Amort_TotalPrincipal - Assump_LoanAmt) < 0.01, "✓ PASS", "✗ FAIL")
```

**Payment Check** (I8):
```excel
=IF(ABS(Amort_TotalPayments - SUM(D13:D372)) < 0.01, "✓ PASS", "✗ FAIL")
```

### Project_Map Health Check

**Health Check Formula** (F2):
```excel
=IF(COUNTIF(Amortization_Schedule!I6:I8,"✗ FAIL")>0, "✗ QA Failed", 
   IF(OR(ISERROR(RateShock_BasePayment), ISERROR(Refi_Decision)), "✗ Formula Error", 
   "✓ All Key Cells OK"))
```

---

## Units and Formatting

| Cell Type | Format | Example |
|---|---|---|
| Mortgage rates (percent) | Percentage, 2 decimals | 6.36% |
| Mortgage rates (decimal) | Number, 6 decimals | 0.063600 |
| Currency | Currency, 2 decimals | $2,491.56 |
| Whole numbers | Number, 0 decimals | 360 |
| Dates | Short Date | 5/14/2026 |
| Month_Year | Text | May 2026 |

---

## References

- **FRED MORTGAGE30US**: https://fred.stlouisfed.org/series/MORTGAGE30US
- **Excel PMT function**: https://support.microsoft.com/en-us/office/pmt-function-0214da64-9a63-4996-bc20-214433fa6441
- **Excel CUMIPMT function**: https://support.microsoft.com/en-us/office/cumipmt-function-61067bb0-9016-427d-b95b-1a752af0e606
- **Excel PV function**: https://support.microsoft.com/en-us/office/pv-function-23879d31-0e02-4321-be01-da16e8168cbd
