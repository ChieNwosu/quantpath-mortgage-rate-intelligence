# Formula Reference

This document provides a comprehensive reference for all Excel formulas used in the Phase 1 workbook, organized by sheet and formula family.

---

## Sign Convention (Critical)

The workbook uses Microsoft's standard cash-flow sign convention for financial functions:

### PMT, PPMT, IPMT

- **Rule**: Use a **negative present value argument** (e.g., `-Assump_LoanAmt`).
- **Result**: The function returns a **positive payment** directly.
- **Do NOT add a leading minus sign to the function itself.**

**Example**:
```excel
=PMT(Assump_BaseRate/12, Assump_LoanTerm, -Assump_LoanAmt)
```

**Incorrect**:
```excel
=-PMT(Assump_BaseRate/12, Assump_LoanTerm, -Assump_LoanAmt)  ❌ WRONG
```

---

### CUMIPMT

- **Rule**: CUMIPMT returns a **negative value** (interest paid out).
- **Result**: Use a **leading minus sign** to convert to positive for display.

**Example**:
```excel
=-CUMIPMT(Assump_BaseRate/12, Assump_LoanTerm, Assump_LoanAmt, 1, Assump_LoanTerm, 0)
```

---

### PV

- **Rule**: PV returns a **negative value** (loan amount).
- **Result**: Use a **leading minus sign** to convert to positive for display.

**Example**:
```excel
=-PV(Assump_BaseRate/12, Assump_LoanTerm, Assump_MonthlyBudget)
```

---

## Rate_Data Sheet

### Helper Columns

**Rate_Decimal** (Column D):
```excel
=C13/100
```
Converts FRED percent rate (e.g., 6.36) to decimal (e.g., 0.0636) for Excel financial functions.

**Year** (Column E):
```excel
=YEAR(B13)
```
Extracts year from DATE column for grouping and filtering.

**Month_Year** (Column F):
```excel
=TEXT(B13,"mmm yyyy")
```
Formats date as "MMM YYYY" (e.g., "May 2026") for chart labels.

**CurrentRateLine** (Column G):
```excel
=IF(B13=MAX(Rate_Data!$B$13:$B$2890),C13,"")
```
Marks the latest rate for chart annotation. Only the most recent row shows the rate value; all others are blank.

---

### Summary Block

**Latest Rate** (B3):
```excel
=INDEX(C:C,COUNTA(C:C))
```
Returns the last non-empty value in column C (MORTGAGE30US).

**Min Rate** (B4):
```excel
=MIN(C13:C2890)
```

**Max Rate** (B5):
```excel
=MAX(C13:C2890)
```

**Average Rate** (B6):
```excel
=AVERAGE(C13:C2890)
```

**52-Week Trailing Average** (B8):
```excel
=AVERAGE(C2838:C2890)
```
Assumes the last 52 rows represent the most recent 52 weeks.

---

## Assumptions Sheet

### Loan Amount Calculation

**Loan Amount** (B6):
```excel
=B4*(1-B5)
```
Calculates loan amount as: Home Price × (1 − Down Payment %).

---

### Base Rate Link

**Base Rate** (B8):
```excel
=RateData_LatestRate
```
Links to the latest rate from the Rate_Data summary block.

---

## Rate_Shock_Model Sheet

### Scenario Rate Column

**Base Case Rate** (C13):
```excel
=Assump_BaseRate
```

**Rate Shock Scenarios** (C14:C18):
```excel
=Assump_BaseRate - 0.01  // -1.00%
=Assump_BaseRate - 0.005 // -0.50%
=Assump_BaseRate + 0.005 // +0.50%
=Assump_BaseRate + 0.01  // +1.00%
=Assump_BaseRate + 0.015 // +1.50%
```

---

### Monthly Payment (PMT)

**Formula** (D13:D18):
```excel
=IFERROR(PMT(C13/12, Assump_LoanTerm, -Assump_LoanAmt), "—")
```

**Explanation**:
- `C13/12`: Monthly rate (annual rate ÷ 12)
- `Assump_LoanTerm`: Number of monthly payments (typically 360)
- `-Assump_LoanAmt`: Negative present value (loan amount)
- `IFERROR(..., "—")`: Display "—" if the formula produces an error

---

### Payment Delta vs. Base

**Formula** (E13:E18):
```excel
=IFERROR(D13 - RateShock_BasePayment, "—")
```

**Explanation**:
- Computes the difference between the scenario payment and the base case payment.
- Base case (row 13) shows $0.00 delta.

---

### Total Lifetime Interest (CUMIPMT)

**Formula** (F13:F18):
```excel
=IFERROR(-CUMIPMT(C13/12, Assump_LoanTerm, Assump_LoanAmt, 1, Assump_LoanTerm, 0), "—")
```

**Explanation**:
- `C13/12`: Monthly rate
- `Assump_LoanTerm`: Number of payments
- `Assump_LoanAmt`: Loan amount (positive)
- `1, Assump_LoanTerm`: Cumulative interest from payment 1 to final payment
- `0`: Payment at end of period (standard)
- Leading minus sign converts negative return value to positive for display

---

### Total Loan Cost

**Formula** (G13:G18):
```excel
=IFERROR(Assump_LoanAmt + F13, "—")
```

**Explanation**:
- Total cost = Principal + Total Interest

---

### Affordable Principal (PV)

**Formula** (H13:H18):
```excel
=IFERROR(-PV(C13/12, Assump_LoanTerm, Assump_MonthlyBudget), "—")
```

**Explanation**:
- `C13/12`: Monthly rate
- `Assump_LoanTerm`: Number of payments
- `Assump_MonthlyBudget`: Fixed monthly payment budget
- Leading minus sign converts negative return value to positive for display
- Answers: "How much can I borrow if my monthly budget is fixed?"

---

## Amortization_Schedule Sheet

### Payment Number

**Formula** (A13:A372):
```excel
=ROW()-12
```
Generates payment numbers 1–360.

---

### Beginning Balance

**First Payment** (B13):
```excel
=Assump_LoanAmt
```

**Subsequent Payments** (B14:B372):
```excel
=F13
```
Links to the ending balance from the previous row.

---

### Monthly Payment

**Formula** (D13:D372):
```excel
=IFERROR(PPMT(Assump_BaseRate/12, A13, Assump_LoanTerm, -Assump_LoanAmt) + IPMT(Assump_BaseRate/12, A13, Assump_LoanTerm, -Assump_LoanAmt), "—")
```

**Explanation**:
- `PPMT(...)`: Principal portion of payment
- `IPMT(...)`: Interest portion of payment
- Sum of PPMT + IPMT = total monthly payment
- Both functions use `-Assump_LoanAmt` (negative present value)

---

### Principal Paid (PPMT)

**Formula** (C13:C372):
```excel
=IFERROR(PPMT(Assump_BaseRate/12, A13, Assump_LoanTerm, -Assump_LoanAmt), "—")
```

**Explanation**:
- `A13`: Payment number (1–360)
- Returns the principal portion of the specified payment

---

### Interest Paid (IPMT)

**Formula** (E13:E372):
```excel
=IFERROR(IPMT(Assump_BaseRate/12, A13, Assump_LoanTerm, -Assump_LoanAmt), "—")
```

**Explanation**:
- Returns the interest portion of the specified payment

---

### Ending Balance

**Formula** (F13:F372):
```excel
=B13 - C13
```

**Explanation**:
- Ending balance = Beginning balance − Principal paid
- Final payment (row 372) should result in ending balance ≈ $0.00

---

### Summary Block

**Total Principal** (B3):
```excel
=SUM(C13:C372)
```
Should equal `Assump_LoanAmt`.

**Total Interest** (B4):
```excel
=SUM(E13:E372)
```

**Total Payments** (B5):
```excel
=SUM(D13:D372)
```
Should equal Total Principal + Total Interest.

---

### QA Checks

**Principal Check** (I6):
```excel
=IF(ABS(Amort_TotalPrincipal - Assump_LoanAmt) < 0.01, "✓ PASS", "✗ FAIL")
```

**Payment Check** (I8):
```excel
=IF(ABS(Amort_TotalPayments - SUM(D13:D372)) < 0.01, "✓ PASS", "✗ FAIL")
```

**Explanation**:
- Checks that the amortization schedule formulas are consistent.
- Tolerance of $0.01 accounts for rounding.

---

## Refinance_Analysis Sheet

### Current Monthly Payment

**Formula** (B13):
```excel
=IFERROR(PMT(Assump_CurrentRate/12, Assump_RemTerm, -Assump_RemBal), "—")
```

**Explanation**:
- Computes the current payment based on the remaining balance, current rate, and remaining term.

---

### Proposed New Payment

**Formula** (B14):
```excel
=IFERROR(PMT(Assump_ProposedRate/12, Assump_RemTerm, -Assump_RemBal), "—")
```

**Explanation**:
- Computes the new payment after refinancing at the proposed rate.

---

### Monthly Savings

**Formula** (B15):
```excel
=IFERROR(Refi_CurrentPayment - Refi_ProposedPayment, "—")
```

**Explanation**:
- Positive value = savings; negative value = refinancing increases payment.

---

### Break-Even Months

**Formula** (B16):
```excel
=IFERROR(Assump_ClosingCosts / Refi_MonthlySavings, "—")
```

**Explanation**:
- Computes how many months it takes for cumulative savings to offset closing costs.
- If monthly savings ≤ 0, the formula returns an error (handled by IFERROR).

---

### Decision Flag

**Formula** (B22):
```excel
=IF(AND(Refi_MonthlySavings > 0, Refi_BreakEvenMonths <= Assump_HoldingPeriod), "Refinance Makes Sense", "Does Not Break Even in Holding Period")
```

**Explanation**:
- "Refinance Makes Sense" if:
  1. Monthly savings > 0, AND
  2. Break-even months ≤ expected holding period
- Otherwise, "Does Not Break Even in Holding Period"

---

## Dashboard Sheet

### KPI Card Formulas

**Latest Mortgage Rate** (B3):
```excel
=RateData_LatestRate
```

**Base Case Monthly Payment** (D3):
```excel
=RateShock_BasePayment
```

**Total Lifetime Interest** (F3):
```excel
=RateShock_BaseTotalInterest
```

**Affordable Loan Amount** (B8):
```excel
=RateShock_BaseAffordable
```

**Refinance Break-Even Months** (D8):
```excel
=Refi_BreakEvenMonths
```

---

### Refinance Summary Table

**Current Payment** (B14):
```excel
=Refi_CurrentPayment
```

**Proposed Payment** (B15):
```excel
=Refi_ProposedPayment
```

**Monthly Savings** (B16):
```excel
=Refi_MonthlySavings
```

**Break-Even Months** (B17):
```excel
=Refi_BreakEvenMonths
```

**Decision** (E14):
```excel
=Refi_Decision
```

---

## Project_Map Sheet

### Workbook Health Check

**Formula** (F2):
```excel
=IF(COUNTIF(Amortization_Schedule!I6:I8,"✗ FAIL")>0, "✗ QA Failed", 
   IF(OR(ISERROR(RateShock_BasePayment), ISERROR(Refi_Decision)), "✗ Formula Error", 
   "✓ All Key Cells OK"))
```

**Explanation**:
- Checks if any Amortization_Schedule QA checks failed.
- Checks if key model cells (RateShock_BasePayment, Refi_Decision) produce errors.
- Returns "✓ All Key Cells OK" only if all checks pass.

---

## Common Patterns

### IFERROR Wrapper

All financial function formulas use `IFERROR(..., "—")` to gracefully handle errors (e.g., division by zero, invalid inputs).

**Pattern**:
```excel
=IFERROR([formula], "—")
```

---

### Monthly Rate Conversion

All financial functions use monthly rate, not annual.

**Pattern**:
```excel
=[AnnualRate]/12
```

---

### Named Range References

All cross-sheet references use named ranges, not hardcoded cell addresses.

**Good**:
```excel
=PMT(Assump_BaseRate/12, Assump_LoanTerm, -Assump_LoanAmt)
```

**Bad**:
```excel
=PMT(Assumptions!B8/12, Assumptions!B7, -Assumptions!B6)  ❌ AVOID
```

---

## Troubleshooting

### Payment Formulas Return Negative Values

**Cause**: Missing the negative sign on the present value argument.

**Fix**: Use `-Assump_LoanAmt`, not `Assump_LoanAmt`.

---

### CUMIPMT or PV Return Negative Values

**Cause**: Missing the leading minus sign to convert the negative return value to positive.

**Fix**: Use `=-CUMIPMT(...)` and `=-PV(...)`.

---

### QA Checks Fail

**Cause**: Formulas were accidentally overwritten or inputs are invalid.

**Fix**:
1. Verify that all Assumptions inputs are valid (positive numbers, reasonable rates).
2. Check that the Rate_Data sheet has data and the latest rate is populated.
3. Ensure no formulas were accidentally overwritten.

---

## References

- **Excel PMT function**: https://support.microsoft.com/en-us/office/pmt-function-0214da64-9a63-4996-bc20-214433fa6441
- **Excel PPMT function**: https://support.microsoft.com/en-us/office/ppmt-function-c370d9e3-7749-4ca4-beea-b06c6ac95e1b
- **Excel IPMT function**: https://support.microsoft.com/en-us/office/ipmt-function-5cce0ad6-8402-4a41-8d29-61a0b054cb6f
- **Excel CUMIPMT function**: https://support.microsoft.com/en-us/office/cumipmt-function-61067bb0-9016-427d-b95b-1a752af0e606
- **Excel PV function**: https://support.microsoft.com/en-us/office/pv-function-23879d31-0e02-4321-be01-da16e8168cbd
