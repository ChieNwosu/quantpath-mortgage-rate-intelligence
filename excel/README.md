# Excel Workbook Guide

**Workbook**: `Rate_Shock_Loan_Affordability_Refinance_Dashboard.xlsx`

This guide explains the structure, usage, and key formulas of the Phase 1 Excel workbook.

---

## Workbook Overview

The workbook is a decision-support tool that translates mortgage rate data into actionable household finance insights. It answers five business questions about affordability, payment sensitivity, lifetime interest burden, and refinance decision-making.

**Key Features**:
- No macros or VBA — works in standard Excel desktop
- All inputs are in the **Assumptions** sheet (yellow cells)
- All outputs recalculate automatically
- 29 workbook-scoped named ranges for maintainability
- Built-in QA checks in **Amortization_Schedule** and **Project_Map**

---

## Sheet-by-Sheet Guide

### 1. Project_Map

**Purpose**: Executive overview and workbook health check

**Key Sections**:
- Project objective and business questions table
- Workbook navigation map (sheet descriptions)
- Formula family summary
- Scope notes (what's included, what's not)
- Portfolio framing statement
- Data source attribution
- Workbook health check formula (cell `F2`)

**How to Use**: Start here to understand the workbook structure and verify all key cells are functioning correctly.

---

### 2. Rate_Data

**Purpose**: Import and prepare the FRED/Freddie Mac mortgage rate dataset

**Key Columns**:
- `DATE`: observation date (weekly Thursday)
- `MORTGAGE30US`: 30-year fixed rate (percent)
- `Rate_Decimal`: converted to decimal for Excel formulas
- `Year`, `Month_Year`: time grouping helpers
- `CurrentRateLine`: marks the latest rate for chart annotation

**Summary Block** (rows 2–8):
- Latest rate
- Historical min, max, average
- 52-week trailing average

**How to Use**: This sheet is read-only. The latest rate automatically feeds into the **Assumptions** sheet via the named range `Assump_BaseRate`.

---

### 3. Assumptions

**Purpose**: Central input panel for all user-configurable parameters

**Input Sections**:

#### Loan Inputs (B4:B9)
- Home price
- Down payment percentage
- Loan amount (calculated)
- Loan term (months)
- Base mortgage rate (from Rate_Data)
- Target monthly payment budget

#### Refinance Inputs (B12:B18)
- Remaining loan balance
- Current mortgage rate
- Proposed new rate
- Remaining term (months)
- Refinance closing costs
- Expected holding period (months)

**How to Use**: Edit the yellow cells to match your scenario. All model sheets and the Dashboard recalculate automatically.

---

### 4. Rate_Shock_Model

**Purpose**: Six-scenario rate shock analysis showing payment and affordability sensitivity

**Scenarios** (rows 13–18):
- Base case (current rate)
- −1.00%, −0.50%, +0.50%, +1.00%, +1.50% rate shocks

**Outputs per Scenario**:
- Monthly payment (PMT)
- Payment delta vs. base case
- Total lifetime interest (CUMIPMT)
- Total loan cost (principal + interest)
- Affordable principal (PV) given target monthly budget

**How to Use**: Compare scenarios to understand how rate changes affect monthly payment and total interest burden. The "Affordable Principal" column answers: "How much can I borrow if my monthly budget is fixed?"

---

### 5. Amortization_Schedule

**Purpose**: Full 360-payment amortization schedule for the base case loan

**Key Columns**:
- Payment number (1–360)
- Beginning balance
- Monthly payment (PPMT + IPMT)
- Principal paid (PPMT)
- Interest paid (IPMT)
- Ending balance

**Summary Block** (rows 2–8):
- Total principal, interest, and payments
- QA checks: formula consistency validation (cells `I6`, `I8`)

**How to Use**: Verify the loan amortizes correctly. The QA checks should show `✓ PASS`. If they fail, the **Project_Map** health check will flag it.

---

### 6. Refinance_Analysis

**Purpose**: Break-even model for refinance decision-making

**Key Outputs** (column B):
- Current monthly payment
- Proposed new payment
- Monthly savings
- Break-even months (closing costs ÷ monthly savings)
- Decision flag: "Refinance Makes Sense" or "Does Not Break Even in Holding Period"

**How to Use**: Adjust refinance inputs in the **Assumptions** sheet. The model computes break-even time and compares it to your expected holding period. If break-even occurs before you plan to sell or refinance again, refinancing is financially attractive.

---

### 7. Dashboard

**Purpose**: Visual summary of key outputs

**KPI Cards** (5 cards):
1. Latest mortgage rate (from Rate_Data)
2. Base case monthly payment
3. Total lifetime interest (base case)
4. Affordable loan amount (given target monthly budget)
5. Refinance break-even months

**Chart Zones** (4 placeholders):
- Historical mortgage rate trend
- Rate shock payment sensitivity
- Amortization schedule balance decline
- Refinance break-even visualization

**Refinance Summary Table**: Current vs. proposed payment, monthly savings, break-even months, decision flag

**How to Use**: This is the primary output sheet. All KPIs and the refinance summary update automatically when you change inputs in the **Assumptions** sheet.

**Note**: The four charts must be inserted manually in Excel. Openpyxl chart support is unreliable for complex series. See the `design.md` spec for chart specifications.

---

## Key Excel Formulas

### Financial Function Sign Convention

The workbook uses Microsoft's standard cash-flow sign convention:

- **PMT, PPMT, IPMT**: Use a negative present value argument (e.g., `-Assump_LoanAmt`). The function returns a positive payment directly. **Do not add a leading minus sign to the function itself.**
  
  Example:
  ```excel
  =PMT(Assump_BaseRate/12, Assump_LoanTerm, -Assump_LoanAmt)
  ```

- **CUMIPMT**: Returns a negative value (interest paid out). Use a leading minus to convert to positive for display.
  
  Example:
  ```excel
  =-CUMIPMT(Assump_BaseRate/12, Assump_LoanTerm, Assump_LoanAmt, 1, Assump_LoanTerm, 0)
  ```

- **PV**: Returns a negative value (loan amount). Use a leading minus to convert to positive for display.
  
  Example:
  ```excel
  =-PV(Assump_BaseRate/12, Assump_LoanTerm, Assump_MonthlyBudget)
  ```

### Named Ranges

The workbook uses 29 workbook-scoped named ranges to eliminate hardcoded cell references. Examples:

- `Assump_HomePrice` → Assumptions!B4
- `Assump_LoanAmt` → Assumptions!B6
- `Assump_BaseRate` → Assumptions!B8
- `Assump_MonthlyBudget` → Assumptions!B9
- `Assump_RemBal` → Assumptions!B12
- `Assump_CurrentRate` → Assumptions!B13
- `Assump_ProposedRate` → Assumptions!B14

See the `design.md` spec for the full named range inventory.

---

## Limitations

- **No macros or automation**: The workbook does not auto-refresh the FRED data. To update the dataset, download the latest CSV from FRED and replace the data in the **Rate_Data** sheet.
- **Manual chart insertion**: The four Dashboard charts must be inserted manually in Excel. Openpyxl chart support is unreliable for complex series.
- **Single loan scenario**: The workbook models one loan at a time. To compare multiple loans, duplicate the workbook or adjust inputs and record outputs separately.
- **No tax or insurance**: The payment calculations include principal and interest only. Property taxes, homeowners insurance, and PMI are not included.
- **No prepayment modeling**: The amortization schedule assumes no extra principal payments.

---

## Troubleshooting

### QA Checks Fail

If **Amortization_Schedule** cells `I6` or `I8` show `✗ FAIL`, or if **Project_Map** cell `F2` shows an error:

1. Verify that the **Assumptions** sheet inputs are valid (positive numbers, reasonable rates).
2. Check that the **Rate_Data** sheet has data and the latest rate is populated.
3. Ensure no formulas were accidentally overwritten.

### Refinance Decision Shows "N/A"

If **Refinance_Analysis** cell `B22` shows `N/A`:

1. Verify that **Assumptions** refinance inputs (B12:B18) are populated.
2. Check that the proposed rate is different from the current rate.
3. Ensure closing costs and holding period are positive numbers.

### Dashboard KPIs Show Errors

If Dashboard KPI cards show `#REF!` or `#VALUE!`:

1. Verify that all named ranges are defined correctly (Formulas → Name Manager).
2. Check that the source sheets (**Rate_Data**, **Rate_Shock_Model**, **Amortization_Schedule**, **Refinance_Analysis**) have not been renamed or deleted.

---

## Next Steps

- **Phase 2 (R)**: Time-series analysis — stationarity testing, rolling statistics, rate-regime analysis, seasonality checks (planned)
- **Phase 3 (SQL)**: Structured data layer for querying and joining with additional economic indicators (planned)

See the [`docs/roadmap.md`](../docs/roadmap.md) for the full multi-phase plan.
