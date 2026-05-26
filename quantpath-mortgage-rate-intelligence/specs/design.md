# Design Document: Rate Shock Loan Affordability & Refinance Dashboard

## Overview

This document is the cell-aware implementation blueprint for the Rate Shock Loan Affordability & Refinance Dashboard Excel workbook. It specifies exact cell ranges, formula syntax, formatting rules, chart configurations, and QA checks for all seven sheets. A developer can implement every sheet purely from this document without making any design decisions.

The workbook answers five business questions:
- **BQ1** — What does the historical rate environment look like, and where is today's rate relative to the full 1971–2026 history?
- **BQ2** — How does a ±1.5% rate shock change the monthly P&I payment on a representative loan?
- **BQ3** — How much total interest does a borrower pay over 30 years at each scenario rate?
- **BQ4** — At a fixed monthly budget, how much loan principal is affordable at each scenario rate?
- **BQ5** — Does refinancing from the current rate to a lower rate break even within the expected holding period?

> **Default Assumptions (Version 1)**: Home Price $500,000 · Down Payment 20% · **Loan Amount $400,000** · Loan Term **30 years fixed (360 months)** · Base Rate linked from Rate_Data · Target Monthly Budget $2,500 · Refinance: Remaining Balance $380,000, Current Rate 7.00%, Proposed Rate 6.00%, Remaining Term 324 months, Closing Costs $6,000, Holding Period 60 months.
>
> All QA benchmarks in Section 11 are labeled **"default-assumptions test only"** and are valid only when Assumptions cells hold these exact values. If any input is changed, the formula-consistency checks (PASS/FAIL cells) remain authoritative; the numeric benchmarks do not.

---

## 1. Workbook Architecture Overview

### Sheet Dependency Order

```
Rate_Data  ──►  Assumptions  ──►  Rate_Shock_Model  ──►  Dashboard
                     │                                        ▲
                     ├──────────►  Amortization_Schedule ─────┤
                     └──────────►  Refinance_Analysis   ──────┘
Project_Map  (static — no formula dependencies)
```

### Data Flow Summary

| Source Sheet | Feeds Into | Key Values Passed |
|---|---|---|
| Rate_Data | Assumptions | Latest Rate (tblMortgageRates summary cell) |
| Assumptions | Rate_Shock_Model | Loan Amount, Base Rate, Fixed 360-month term, Budget |
| Assumptions | Amortization_Schedule | Loan Amount, Base Rate, Fixed 360-month term |
| Assumptions | Refinance_Analysis | All refinance inputs (editable remaining term) |
| Rate_Shock_Model | Dashboard | All 6 scenario rows |
| Amortization_Schedule | Dashboard | Summary totals |
| Refinance_Analysis | Dashboard | Break-even, savings, decision flag |

### Sheet Tab Order (left to right)
`Project_Map` → `Rate_Data` → `Assumptions` → `Rate_Shock_Model` → `Amortization_Schedule` → `Refinance_Analysis` → `Dashboard`

---

## 2. Workbook-Wide Formatting and Style Rules

### Font
- **Font family**: Calibri throughout all sheets
- **Base size**: 11pt for body/data cells
- **Header size**: 14pt for sheet title banners; 12pt for section headers; 11pt bold for column headers

### Color Palette

| Role | Fill Color | Font Color | Notes |
|---|---|---|---|
| Sheet title banner | `#1F3864` (dark navy) | `#FFFFFF` (white) | Row 1 merged banner |
| Section header | `#2E75B6` (medium blue) | `#FFFFFF` (white) | Bold, 12pt |
| Column header row | `#D6E4F0` (light blue) | `#1F3864` (dark navy) | Bold, 11pt |
| Input Cell | `#FFF2CC` (light yellow) | `#000000` (black) | User-editable |
| Formula Cell | `#FFFFFF` (white) | `#000000` (black) | No fill; locked |
| Base Case row highlight | `#E2EFDA` (light green) | `#000000` (black) | Rate_Shock_Model only |
| KPI card background | `#EBF3FB` (pale blue) | `#1F3864` (dark navy) | Dashboard only |
| Warning / error flag | `#FFE0E0` (pale red) | `#C00000` (dark red) | Decision flag "Does Not Break Even" |
| Positive flag | `#E2EFDA` (light green) | `#375623` (dark green) | Decision flag "Refinance Makes Sense" |

### Number Formats

| Data Type | Excel Format Code | Example |
|---|---|---|
| Currency (2 dp) | `$#,##0.00` | $2,147.29 |
| Currency (0 dp) | `$#,##0` | $400,000 |
| Percentage (2 dp) | `0.00%` | 6.36% |
| Percentage (4 dp decimal) | `0.0000%` | applied to Rate_Decimal column |
| Integer count | `#,##0` | 360 |
| Month count | `#,##0` | 27 |
| Date | `MMM D, YYYY` | Apr 2, 1971 |
| Text label | General | — |

### Cell Protection Convention
- All Input Cells: **unlocked** (Format Cells → Protection → Locked = unchecked)
- All Formula Cells: **locked** (default)
- Sheet protection applied after build; allow selecting unlocked cells only

---

## 3. Sheet Design: Rate_Data

### Business Question Answered
**BQ1** — Historical rate context: where is today's rate relative to the full 1971–2026 history?

### Rate_Data Table Strategy: Convert to Excel Table (`tblMortgageRates`)

**Decision**: Convert the imported CSV range to a named Excel Table. This eliminates all hardcoded row references, makes formulas self-extending when new data is appended, and enables structured references throughout the workbook.

**How to convert**: Click any cell in columns A or B of Rate_Data, then use Ctrl+Shift+End to confirm the last occupied row. Select from A1 down to the last occupied row in column B (the full contiguous range — currently A1:B2878 but may grow as data is appended) → Insert → Table → confirm "My table has headers" → OK → rename the table to `tblMortgageRates` in the Table Design tab. This approach remains correct regardless of the exact row count.

After conversion:
- Column A becomes `tblMortgageRates[observation_date]`
- Column B becomes `tblMortgageRates[MORTGAGE30US]`
- Helper columns C, D, E added to the table automatically extend when rows are appended
- Summary formulas use structured references instead of hardcoded row ranges

**Do not modify any existing values in columns A or B.**

### Layout Zones

| Zone | Range | Description |
|---|---|---|
| Table (after conversion) | A1 through last occupied row in cols A:B | `tblMortgageRates` with 5 columns |
| Helper column: Rate_Decimal | C (table column) | New column added to table |
| Helper column: Year | D (table column) | New column added to table |
| Helper column: Month_Year | E (table column) | New column added to table |
| Summary block header | G1 | Section label |
| Summary block data | G2:H8 | 6 statistics + labels |
| Reference line helper | F (table column) | Constant = latest rate, for Dashboard chart |

### Table Column Headers

| Column | Header Name | Notes |
|---|---|---|
| A | `observation_date` | Existing — do not rename |
| B | `MORTGAGE30US` | Existing — do not rename |
| C | `Rate_Decimal` | New |
| D | `Year` | New |
| E | `Month_Year` | New |
| F | `CurrentRateLine` | New — used as chart reference line |

### Helper Column Formulas (enter in row 2; table auto-fills down)

**C2 — Rate_Decimal**:
```excel
=[@MORTGAGE30US]/100
```
Format: `0.0000`

**D2 — Year**:
```excel
=YEAR([@observation_date])
```
Format: `General`

**E2 — Month_Year**:
```excel
=TEXT([@observation_date],"MMM-YYYY")
```
Format: `General`

**F2 — CurrentRateLine** (constant reference line for Dashboard chart):
```excel
=Rate_Data!$H$2
```
Format: `0.00`

> Column F holds the same value (latest rate) in every row, creating a flat horizontal reference line when plotted alongside the historical rate series on the Dashboard chart.

### Summary Block (G1:H8)

**G1** — Section header: `"Rate Summary Statistics"` — Section Header style

| Cell | Label (col G) | Formula (col H) | Format |
|---|---|---|---|
| G2 | `Latest Rate` | `=INDEX(tblMortgageRates[MORTGAGE30US],MATCH(MAX(tblMortgageRates[observation_date]),tblMortgageRates[observation_date],0))` | `0.00` |
| G3 | `Historical Minimum` | `=MIN(tblMortgageRates[MORTGAGE30US])` | `0.00` |
| G4 | `Historical Maximum` | `=MAX(tblMortgageRates[MORTGAGE30US])` | `0.00` |
| G5 | `Historical Average` | `=AVERAGE(tblMortgageRates[MORTGAGE30US])` | `0.00` |
| G6 | `52-Week Trailing Avg` | `=AVERAGE(OFFSET(INDEX(tblMortgageRates[MORTGAGE30US],ROWS(tblMortgageRates[MORTGAGE30US])),0,0,-52,1))` | `0.00` |
| G7 | `Data Start` | `=TEXT(MIN(tblMortgageRates[observation_date]),"MMM D, YYYY")` | `General` |
| G8 | `Data End` | `=TEXT(MAX(tblMortgageRates[observation_date]),"MMM D, YYYY")` | `General` |

> **Format note**: Summary block stores rates as raw percentage numbers (e.g., 6.36, not 0.0636). Format H2:H6 as `0.00` so they display as "6.36" not "636.00%". The Assumptions sheet divides by 100 when linking to this block.

> **Latest Rate formula**: Uses INDEX/MATCH on the date column to find the row with the maximum (most recent) date. Robust to future data appends — no hardcoded row number.

> **52-Week Trailing Average**: Uses OFFSET from the last row of the table, going back 52 rows (52 weekly observations ≈ 1 year). Automatically adjusts as rows are appended.

**Named Range**: Define `LatestRate` = `Rate_Data!$H$2` for use in Assumptions.

### Data Validation
None required on Rate_Data (read-only data sheet).

### Freeze Panes
Freeze row 1 (table header row) so data scrolls beneath.

---

## 4. Sheet Design: Assumptions

### Business Question Answered
Central input hub — feeds all model sheets. No direct BQ output, but controls all BQ1–BQ5 calculations.

### Loan Term Design Decision: 30-Year Fixed for New-Purchase Model

**Rationale**: The source dataset is the FRED MORTGAGE30US series — the 30-Year Fixed Rate Mortgage Average. The Rate_Shock_Model and Amortization_Schedule are conceptually tied to this series. Allowing an editable loan term for the new-purchase model would create a mismatch between the rate series label and the model term.

**Version 1 decision**: The new-purchase loan term is **fixed at 30 years (360 months)** in the model. `Assump_TermYrs` (B7) is displayed as a read-only formula cell showing `=30`, not a user input. `Assump_TermMos` (B8) = `=B7*12` = 360.

This keeps Rate_Shock_Model and Amortization_Schedule conceptually clean and aligned with the 30-year fixed rate source data.

The **Refinance remaining term** (B17) remains a fully editable Input Cell, since refinance scenarios legitimately vary by how many months remain on the original loan.

### Layout Zones

| Zone | Range | Description |
|---|---|---|
| Sheet title banner | A1:D1 (merged) | "Assumptions — Input Control Panel" |
| Loan Inputs section header | A3 | "LOAN INPUTS" |
| Loan input labels | A4:A10 | Left-aligned labels |
| Loan input values | B4:B10 | Input or formula cells |
| Refinance Inputs section header | A13 | "REFINANCE INPUTS" |
| Refinance input labels | A14:A20 | Left-aligned labels |
| Refinance input values | B14:B20 | Input cells |
| Notes column | D4:D20 | Helper text / units |

### Loan Inputs Block (A3:D10)

**A3** — Section header: `"LOAN INPUTS"` — Section Header style

| Row | Cell A (Label) | Cell B (Value) | Cell Type | Default Value | Format | Notes (col D) |
|---|---|---|---|---|---|---|
| 4 | `Home Price` | B4 | **Input** | `500000` | `$#,##0` | "Enter purchase price" |
| 5 | `Down Payment %` | B5 | **Input** | `0.20` | `0.00%` | "Enter as decimal (e.g., 0.20)" |
| 6 | `Loan Amount` | B6 | **Formula** | `=B4*(1-B5)` | `$#,##0` | "Computed: Home Price × (1 − DP%)" |
| 7 | `Loan Term (years)` | B7 | **Formula** | `=30` | `#,##0` | "Fixed: 30-yr fixed rate model" |
| 8 | `Loan Term (months)` | B8 | **Formula** | `=B7*12` | `#,##0` | "Computed: 360 months" |
| 9 | `Base Mortgage Rate` | B9 | **Formula** | `=LatestRate/100` | `0.00%` | "Auto-linked from Rate_Data" |
| 10 | `Target Monthly Budget` | B10 | **Input** | `2500` | `$#,##0` | "Max monthly P&I payment" |

> **B7 note**: Displayed as a formula `=30` (Formula Cell style, locked) to make the 30-year fixed constraint visible and intentional. A future version could make this editable if ARM or 15-year scenarios are added.

> **B9 formula**: `=LatestRate/100` — divides the raw percentage value (e.g., 6.36) by 100 to produce a decimal (0.0636) for use in PMT/PV functions.

### Refinance Inputs Block (A13:D20)

**A13** — Section header: `"REFINANCE INPUTS"` — Section Header style

| Row | Cell A (Label) | Cell B (Value) | Cell Type | Default Value | Format | Notes (col D) |
|---|---|---|---|---|---|---|
| 14 | `Remaining Balance` | B14 | **Input** | `380000` | `$#,##0` | "Current outstanding principal" |
| 15 | `Current Mortgage Rate` | B15 | **Input** | `0.07` | `0.00%` | "Enter as decimal (e.g., 0.07)" |
| 16 | `Proposed Refinance Rate` | B16 | **Input** | `0.06` | `0.00%` | "Enter as decimal (e.g., 0.06)" |
| 17 | `Remaining Term (months)` | B17 | **Input** | `324` | `#,##0` | "Months left on current loan" |
| 18 | `Closing Costs` | B18 | **Input** | `6000` | `$#,##0` | "Total refi closing costs" |
| 19 | `Expected Holding Period` | B19 | **Input** | `60` | `#,##0` | "Months you plan to stay" |

### Data Validation Rules

**B5 — Down Payment %**: Decimal, 0 to 1. Error: "Enter as decimal between 0 and 1 (e.g., 0.20 for 20%)."

**B15 — Current Mortgage Rate**: Decimal, 0.001 to 0.30. Error: "Enter rate as decimal (e.g., 0.07 for 7%)."

**B16 — Proposed Refinance Rate**: Same as B15.

**B17 — Remaining Term (months)**: Whole number, 1 to 360. Error: "Enter months remaining 1–360."

**B19 — Expected Holding Period**: Whole number, 1 to 360. Error: "Enter holding period 1–360 months."

> **B7 (Loan Term)** has no data validation because it is a Formula Cell, not an Input Cell.

### Named Ranges to Define

| Name | Refers To |
|---|---|
| `Assump_HomePrice` | `=Assumptions!$B$4` |
| `Assump_DPPct` | `=Assumptions!$B$5` |
| `Assump_LoanAmt` | `=Assumptions!$B$6` |
| `Assump_TermYrs` | `=Assumptions!$B$7` |
| `Assump_TermMos` | `=Assumptions!$B$8` |
| `Assump_BaseRate` | `=Assumptions!$B$9` |
| `Assump_Budget` | `=Assumptions!$B$10` |
| `Assump_RemBal` | `=Assumptions!$B$14` |
| `Assump_CurrRate` | `=Assumptions!$B$15` |
| `Assump_PropRate` | `=Assumptions!$B$16` |
| `Assump_RemTerm` | `=Assumptions!$B$17` |
| `Assump_ClosingCosts` | `=Assumptions!$B$18` |
| `Assump_HoldPeriod` | `=Assumptions!$B$19` |

### Column Widths
- Column A: 28, Column B: 18, Column C: 2 (spacer), Column D: 35

---

## 5. Sheet Design: Rate_Shock_Model

### Business Questions Answered
- **BQ2** — How does monthly payment change across ±1.5% rate scenarios?
- **BQ3** — How much total interest is paid over 30 years at each scenario rate?
- **BQ4** — How much can a borrower afford at a fixed monthly budget at each rate?

### Loan Term Note
All Rate_Shock_Model formulas use `Assump_TermMos` (= 360, fixed). This is intentional — the model is aligned with the 30-year fixed rate source series. The term does not vary across scenarios.

### Layout Zones

| Zone | Range | Description |
|---|---|---|
| Sheet title banner | A1:H1 (merged) | "Rate Shock Scenario Analysis" |
| Input reference block | A2:B6 | Read-only display of key Assumptions inputs |
| Column headers | A10:H10 | Scenario table headers |
| Scenario data rows | A11:H16 | 6 scenario rows |

### Input Reference Block (A2:B6)

**A2** — Section header: `"Key Inputs (from Assumptions)"` — Section Header style

| Row | A (Label) | B (Value) | Format |
|---|---|---|---|
| 3 | `Loan Amount` | `=Assump_LoanAmt` | `$#,##0` |
| 4 | `Base Rate` | `=Assump_BaseRate` | `0.00%` |
| 5 | `Loan Term (months)` | `=Assump_TermMos` | `#,##0` |
| 6 | `Monthly Budget` | `=Assump_Budget` | `$#,##0` |

### Scenario Table Column Headers (Row 10)

| Cell | Header Text |
|---|---|
| A10 | `Scenario` |
| B10 | `Rate Offset` |
| C10 | `Scenario Rate` |
| D10 | `Monthly Payment` |
| E10 | `Δ Payment vs. Base` |
| F10 | `Total Lifetime Interest` |
| G10 | `Total Loan Cost` |
| H10 | `Affordable Principal` |

### Scenario Rows (A11:H16)

**Column A — Scenario Labels** (text, manually entered):

| Row | Value |
|---|---|
| 11 | `Rate −1.00%` |
| 12 | `Rate −0.50%` |
| 13 | `Base Case` |
| 14 | `Rate +0.50%` |
| 15 | `Rate +1.00%` |
| 16 | `Rate +1.50%` |

**Column B — Rate Offsets** (manually entered constants, format `0.00%`):
`-0.01`, `-0.005`, `0`, `0.005`, `0.01`, `0.015`

**Column C — Scenario Rate** (format `0.00%`):
```excel
C11: =Assump_BaseRate+B11   [through C16: =Assump_BaseRate+B16]
```

**Column D — Monthly Payment** (PMT, format `$#,##0.00`):
```excel
D11: =IFERROR(PMT(C11/12, Assump_TermMos, -Assump_LoanAmt), "—")
```
*(same pattern D12:D16 using C12:C16)*

> PMT with a negative pv argument returns a positive payment directly.

**Column E — Payment Change vs. Base** (format `$#,##0.00`):
```excel
E11: =IFERROR(D11-$D$13, "—")
E13: =0
```
*(E12, E14:E16 follow same pattern)*

Apply conditional formatting to E11:E16: negative values → green font `#375623`; positive values → red font `#C00000`.

**Column F — Total Lifetime Interest** (CUMIPMT, format `$#,##0`):
```excel
F11: =IFERROR(-CUMIPMT(C11/12, Assump_TermMos, Assump_LoanAmt, 1, Assump_TermMos, 0), "—")
```
*(same pattern F12:F16)*

> CUMIPMT returns a negative value; negate with leading minus sign to display as positive.

**Column G — Total Loan Cost** (format `$#,##0`):
```excel
G11: =IFERROR(Assump_LoanAmt+F11, "—")
```
*(same pattern G12:G16)*

**Column H — Affordable Principal** (PV, format `$#,##0`):
```excel
H11: =IFERROR(-PV(C11/12, Assump_TermMos, Assump_Budget), "—")
```
*(same pattern H12:H16)*

> PV returns a negative value; negate to display as positive principal.

### Base Case Row Formatting
Apply Base Case highlight (light green fill `#E2EFDA`, bold font) to **A13:H13**.

### Named Ranges for Dashboard

| Name | Refers To |
|---|---|
| `RSM_BasePayment` | `=Rate_Shock_Model!$D$13` |
| `RSM_BaseTotalInterest` | `=Rate_Shock_Model!$F$13` |
| `RSM_BaseAffordable` | `=Rate_Shock_Model!$H$13` |
| `RSM_Labels` | `=Rate_Shock_Model!$A$11:$A$16` |
| `RSM_Payments` | `=Rate_Shock_Model!$D$11:$D$16` |
| `RSM_TotalInterest` | `=Rate_Shock_Model!$F$11:$F$16` |
| `RSM_Affordable` | `=Rate_Shock_Model!$H$11:$H$16` |

### Column Widths
A: 16, B: 12, C: 14, D: 16, E: 20, F: 22, G: 18, H: 20

---

## 6. Sheet Design: Amortization_Schedule

### Business Questions Answered
Supports **BQ2** and **BQ3** — full month-by-month mechanics of the base-case 30-year fixed loan.

### Loan Term Note
The schedule always runs exactly 360 rows (months 1–360), matching the fixed 30-year term. `Assump_TermMos` is always 360. No conditional row-hiding or dynamic row count is needed in Version 1.

### Layout Zones

| Zone | Range | Description |
|---|---|---|
| Sheet title banner | A1:F1 (merged) | "Base-Case Loan Amortization Schedule" |
| Input reference block | A2:B6 | Read-only display of key inputs |
| Summary block | H2:I8 | Totals + PASS/FAIL checks |
| Column headers | A9:F9 | Table headers |
| Data rows | A10:F369 | 360 rows (Month 1 = row 10, Month 360 = row 369) |

### Input Reference Block (A2:B6)

**A2** — Section header: `"Loan Parameters"` — Section Header style

| Row | A (Label) | B (Value) | Format |
|---|---|---|---|
| 3 | `Loan Amount` | `=Assump_LoanAmt` | `$#,##0` |
| 4 | `Annual Rate` | `=Assump_BaseRate` | `0.00%` |
| 5 | `Monthly Rate` | `=Assump_BaseRate/12` | `0.0000%` |
| 6 | `Term (months)` | `=Assump_TermMos` | `#,##0` |

### Summary Block (H2:I8)

**H2** — Section header: `"Schedule Totals"` — Section Header style

| Row | H (Label) | I (Formula) | Format |
|---|---|---|---|
| 3 | `Total Payments` | `=SUM(C10:C369)` | `$#,##0.00` |
| 4 | `Total Principal Paid` | `=SUM(D10:D369)` | `$#,##0.00` |
| 5 | `Total Interest Paid` | `=SUM(E10:E369)` | `$#,##0.00` |
| 6 | `Principal = Loan Amt?` | `=IF(ABS(I4-Assump_LoanAmt)<1,"✓ PASS","✗ FAIL")` | `General` |
| 7 | `Ending Balance (Mo 360)` | `=F369` | `$#,##0.00` |
| 8 | `Balance ≈ $0?` | `=IF(ABS(F369)<1,"✓ PASS","✗ FAIL")` | `General` |

> The PASS/FAIL cells (I6, I8) are the authoritative correctness checks. They remain valid regardless of what input values are in Assumptions.

### Column Headers (Row 9)

| Cell | Header |
|---|---|
| A9 | `Payment #` |
| B9 | `Beginning Balance` |
| C9 | `Monthly Payment` |
| D9 | `Principal Paid` |
| E9 | `Interest Paid` |
| F9 | `Ending Balance` |

### Row 10 Formulas (Month 1 — Seed Row)

```excel
A10: =1
B10: =Assump_LoanAmt
C10: =IFERROR(-PMT(Assump_BaseRate/12, Assump_TermMos, -Assump_LoanAmt), 0)
D10: =IFERROR(-PPMT(Assump_BaseRate/12, A10, Assump_TermMos, -Assump_LoanAmt), 0)
E10: =IFERROR(-IPMT(Assump_BaseRate/12, A10, Assump_TermMos, -Assump_LoanAmt), 0)
F10: =IFERROR(B10-D10, 0)
```

### Row 11 Formulas (Month 2 — Repeating Pattern)

```excel
A11: =A10+1
B11: =F10
C11: =IFERROR(-PMT(Assump_BaseRate/12, Assump_TermMos, -Assump_LoanAmt), 0)
D11: =IFERROR(-PPMT(Assump_BaseRate/12, A11, Assump_TermMos, -Assump_LoanAmt), 0)
E11: =IFERROR(-IPMT(Assump_BaseRate/12, A11, Assump_TermMos, -Assump_LoanAmt), 0)
F11: =IFERROR(B11-D11, 0)
```

**Copy-down**: Select A11:F11, copy down through row 369. The period argument in PPMT/IPMT (`A11`) auto-increments. The Beginning Balance (`=F10`) shifts correctly. All Assump_ named ranges are absolute.

### Named Ranges for Dashboard

| Name | Refers To |
|---|---|
| `Amort_TotalPayments` | `=Amortization_Schedule!$I$3` |
| `Amort_TotalPrincipal` | `=Amortization_Schedule!$I$4` |
| `Amort_TotalInterest` | `=Amortization_Schedule!$I$5` |

### Freeze Panes
Freeze rows 1–9 so column headers remain visible while scrolling.

### Column Widths
A: 12, B: 18, C: 18, D: 16, E: 16, F: 18, G: 4 (spacer), H: 28, I: 18

---

## 7. Sheet Design: Refinance_Analysis

### Business Question Answered
**BQ5** — Does refinancing from the current rate to a lower rate break even within the expected holding period?

### Layout Zones

| Zone | Range | Description |
|---|---|---|
| Sheet title banner | A1:D1 (merged) | "Refinance Break-Even Analysis" |
| Input display block | A2:B9 | Read-only references to Assumptions |
| Results block | A12:B20 | Computed outputs |
| Decision flag | A22:C23 | Prominent decision output |

### Input Display Block (A2:B9)

**A2** — Section header: `"Refinance Inputs (from Assumptions)"` — Section Header style

| Row | A (Label) | B (Value) | Format |
|---|---|---|---|
| 3 | `Remaining Balance` | `=Assump_RemBal` | `$#,##0` |
| 4 | `Current Mortgage Rate` | `=Assump_CurrRate` | `0.00%` |
| 5 | `Proposed Refinance Rate` | `=Assump_PropRate` | `0.00%` |
| 6 | `Remaining Term (months)` | `=Assump_RemTerm` | `#,##0` |
| 7 | `Closing Costs` | `=Assump_ClosingCosts` | `$#,##0` |
| 8 | `Expected Holding Period` | `=Assump_HoldPeriod` | `#,##0` |

### Results Block (A12:B20)

**A12** — Section header: `"Refinance Analysis Results"` — Section Header style

| Row | A (Label) | B (Formula) | Format |
|---|---|---|---|
| 13 | `Current Monthly Payment` | `=IFERROR(-PMT(Assump_CurrRate/12, Assump_RemTerm, -Assump_RemBal), "—")` | `$#,##0.00` |
| 14 | `New Monthly Payment` | `=IFERROR(-PMT(Assump_PropRate/12, Assump_RemTerm, -Assump_RemBal), "—")` | `$#,##0.00` |
| 15 | `Monthly Savings` | `=IFERROR(B13-B14, "—")` | `$#,##0.00` |
| 16 | `Break-Even Months` | *(see formula below)* | `General` |
| 17 | `Expected Holding Period` | `=Assump_HoldPeriod` | `#,##0` |
| 18 | `Total Savings Over Holding Period` | `=IFERROR(IF(ISNUMBER(B15),B15*Assump_HoldPeriod,"—"), "—")` | `$#,##0` |
| 19 | `Net Benefit After Closing Costs` | `=IFERROR(IF(ISNUMBER(B18),B18-Assump_ClosingCosts,"—"), "—")` | `$#,##0` |

**B16 — Break-Even Months** (zero-savings guard):
```excel
=IFERROR(
  IF(OR(NOT(ISNUMBER(B15)), B15<=0),
    "No Savings — Refinance Not Beneficial",
    Assump_ClosingCosts/B15
  ),
  "Error"
)
```
Format: `General` (accommodates both numeric and text outputs)

### Decision Flag (A22:C23)

**A21** — Section header: `"Decision"` — Section Header style, 14pt

**B22 — Decision Flag**:
```excel
=IF(OR(NOT(ISNUMBER(B15)), B15<=0),
  "No Savings — Refinance Not Beneficial",
  IF(ISNUMBER(B16),
    IF(B16<=Assump_HoldPeriod,
      "Refinance Makes Sense",
      "Does Not Break Even in Holding Period"
    ),
    "No Savings — Refinance Not Beneficial"
  )
)
```

Merge **B22:C22**, 14pt bold, center-aligned.

**Conditional Formatting on B22**:
- `"Refinance Makes Sense"` → Fill `#E2EFDA`, Font `#375623`, Bold
- Contains `"Does Not Break Even"` → Fill `#FFE0E0`, Font `#C00000`, Bold
- Contains `"No Savings"` → Fill `#FFE0E0`, Font `#C00000`, Bold

### Named Ranges for Dashboard

| Name | Refers To |
|---|---|
| `Refi_CurrPayment` | `=Refinance_Analysis!$B$13` |
| `Refi_NewPayment` | `=Refinance_Analysis!$B$14` |
| `Refi_MonthlySavings` | `=Refinance_Analysis!$B$15` |
| `Refi_BreakEvenMos` | `=Refinance_Analysis!$B$16` |
| `Refi_DecisionFlag` | `=Refinance_Analysis!$B$22` |

### Column Widths
A: 30, B: 22, C: 20

---

## 8. Sheet Design: Dashboard

### Business Questions Answered
All five BQs — executive visual summary in a single non-scrolling view.

### Layout Philosophy
Display-only sheet. No Input Cells. All values are formula references or chart data series. Target: fits within columns A–P, rows 1–65 at 100% zoom on a 1920×1080 display.

### Layout Zones

| Zone | Range | Description |
|---|---|---|
| Title banner | A1:P1 (merged) | Dashboard title |
| KPI Cards | A3:P10 | 5 KPI cards side by side |
| Chart row 1 left | A12:H30 | Historical Rate Trend |
| Chart row 1 right | I12:P30 | Payment Sensitivity |
| Chart row 2 left | A32:H50 | Total Interest by Scenario |
| Chart row 2 right | I32:P50 | Affordable Loan Amount by Scenario |
| Refinance summary | A52:P62 | Refinance break-even formatted table |

### Title Banner (A1:P1)
Merged, text: `"Rate Shock Loan Affordability & Refinance Dashboard"`, dark navy fill, white 16pt bold Calibri, center-aligned, row height 36pt.

---

## 8.1 KPI Card Specifications

Five cards arranged horizontally. Each card: 3 columns × 8 rows. Internal structure: row 4 = label, rows 5–7 = value (28pt bold), row 8 = source note (9pt italic gray).

### KPI Card 1 — Latest Mortgage Rate
- **Range**: A3:C10 · Fill: `#EBF3FB`
- **Label (A4:C4)**: `"Latest 30-Yr Fixed Rate"`
- **Value (A5:C7)**: `=TEXT(Rate_Data!H2,"0.00")&"%"` · 28pt bold dark navy
- **Source note (A8:C8)**: `="Source: FRED · "&TEXT(MAX(tblMortgageRates[observation_date]),"MMM D, YYYY")`

### KPI Card 2 — Base Case Monthly Payment
- **Range**: D3:F10 · Fill: `#EBF3FB`
- **Label (D4:F4)**: `"Base Case Monthly P&I"`
- **Value (D5:F7)**: `=RSM_BasePayment` · Format: `$#,##0.00` · 28pt bold dark navy
- **Source note (D8:F8)**: `="$"&TEXT(Assump_LoanAmt/1000,"#,##0")&"K loan · 30 yrs · "&TEXT(Assump_BaseRate,"0.00%")`

### KPI Card 3 — Total Lifetime Interest
- **Range**: G3:I10 · Fill: `#EBF3FB`
- **Label (G4:I4)**: `"Total Lifetime Interest"`
- **Value (G5:I7)**: `=RSM_BaseTotalInterest` · Format: `$#,##0` · 28pt bold dark navy
- **Source note (G8:I8)**: `"Base case · 30-year term"`

### KPI Card 4 — Affordable Loan at Budget
- **Range**: J3:L10 · Fill: `#EBF3FB`
- **Label (J4:L4)**: `="Affordable Loan @ $"&TEXT(Assump_Budget/1000,"#,##0")&"K/mo"`
- **Value (J5:L7)**: `=RSM_BaseAffordable` · Format: `$#,##0` · 28pt bold dark navy
- **Source note (J8:L8)**: `"At base rate · 30-yr term"`

### KPI Card 5 — Refinance Break-Even
- **Range**: M3:P10 · Fill: `#EBF3FB`
- **Label (M4:P4)**: `"Refi Break-Even"`
- **Value (M5:P7)**: `=IF(ISNUMBER(Refi_BreakEvenMos),TEXT(Refi_BreakEvenMos,"0.0")&" months","N/A")` · 28pt bold dark navy
- **Source note (M8:P8)**: `=TEXT(Assump_CurrRate,"0.00%")&" → "&TEXT(Assump_PropRate,"0.00%")&" · $"&TEXT(Assump_ClosingCosts/1000,"#,##0")&"K closing"`
- **Decision flag (M9:P10)**: `=Refi_DecisionFlag` · 10pt bold · same conditional formatting as Refinance_Analysis B22

---

## 8.2 Chart Specifications

### Chart 1 — Historical Mortgage Rate Trend

| Property | Value |
|---|---|
| Chart type | Line (no markers) |
| Placement | A12:H30 |
| Title | `"30-Year Fixed Mortgage Rate — Historical Trend (1971–2026)"` |
| X-axis data | `tblMortgageRates[observation_date]` |
| Y-axis data (series 1) | `tblMortgageRates[MORTGAGE30US]` · blue `#2E75B6` · 1.5pt |
| Y-axis data (series 2) | `tblMortgageRates[CurrentRateLine]` · dashed orange `#ED7D31` · labeled "Current Rate" |
| Y-axis min/max | 0 to 20 |
| Y-axis label | `"Rate (%)"` |
| Gridlines | Horizontal major only, light gray `#D9D9D9` |
| Legend | Show (two series: "30-Yr Fixed Rate" and "Current Rate") |
| Chart area fill | `#F2F7FC` |

> Series 2 uses the `CurrentRateLine` column added to `tblMortgageRates` in Section 3, which holds `=Rate_Data!$H$2` for every row — producing a flat horizontal reference line at the current rate.

### Chart 2 — Payment Sensitivity by Rate Scenario

| Property | Value |
|---|---|
| Chart type | Clustered column |
| Placement | I12:P30 |
| Title | `"Monthly P&I Payment by Rate Scenario"` |
| Category axis | `RSM_Labels` (scenario labels) |
| Values | `RSM_Payments` (Monthly Payment) |
| Y-axis label | `"Monthly Payment ($)"` · format `$#,##0` |
| Base Case bar color | `#375623` (dark green); all others `#2E75B6` (blue) |
| Data labels | Value above each bar, format `$#,##0` |
| Chart area fill | `#F2F7FC` |

### Chart 3 — Total Lifetime Interest by Scenario

| Property | Value |
|---|---|
| Chart type | Clustered column |
| Placement | A32:H50 |
| Title | `"Total Lifetime Interest by Rate Scenario"` |
| Category axis | `RSM_Labels` |
| Values | `RSM_TotalInterest` |
| Y-axis label | `"Total Interest ($)"` · format `$#,##0` |
| Base Case bar color | `#375623`; others `#ED7D31` (orange) |
| Data labels | Value above each bar, format `$#,##0` |
| Chart area fill | `#F2F7FC` |

### Chart 4 — Affordable Loan Amount by Scenario

| Property | Value |
|---|---|
| Chart type | Clustered column |
| Placement | I32:P50 |
| Title | `"Affordable Loan Amount at Fixed Monthly Budget"` |
| Category axis | `RSM_Labels` |
| Values | `RSM_Affordable` |
| Y-axis label | `"Affordable Principal ($)"` · format `$#,##0` |
| Base Case bar color | `#375623`; others `#2E75B6` (blue) |
| Data labels | Value above each bar, format `$#,##0` |
| Chart area fill | `#F2F7FC` |

### Refinance Break-Even Summary Table (A52:D62)

**A51** — Section header: `"Refinance Break-Even Summary"` — Section Header style

| Cell A (Label) | Cell B (Formula) | Format |
|---|---|---|
| `Current Payment` | `=Refi_CurrPayment` | `$#,##0.00` |
| `New Payment` | `=Refi_NewPayment` | `$#,##0.00` |
| `Monthly Savings` | `=Refi_MonthlySavings` | `$#,##0.00` |
| `Break-Even` | `=IF(ISNUMBER(Refi_BreakEvenMos),TEXT(Refi_BreakEvenMos,"0.0")&" months","N/A")` | `General` |
| `Holding Period` | `=TEXT(Assump_HoldPeriod,"#,##0")&" months"` | `General` |
| `Decision` | `=Refi_DecisionFlag` | `General` + conditional formatting |

Apply KPI card fill `#EBF3FB` to block A52:D62. Apply same conditional formatting to Decision cell as Refinance_Analysis B22.

---

## 9. Sheet Design: Project_Map

### Role
Deliverable-ready workbook landing page. A portfolio reviewer should be able to understand the entire project without opening any other sheet. Contains no Input Cells and no formula-driven model outputs.

### Preservation Notes
The sheet already has a title banner, dataset/context block, objective section, and business-question table headers. **Do not delete any existing content.** Polish existing sections to match the workbook style, then add the missing sections below.

### Layout Zones

| Zone | Range | Description |
|---|---|---|
| Title banner | A1:F1 (merged) | Already exists — verify style |
| Context block | A3:F4 | Already exists — verify accuracy |
| Project Objective | A6:F8 | Already exists — verify completeness |
| Business Questions table | A10:E16 | Headers exist — complete rows |
| Workbook Navigation Map | A18:E26 | New section |
| Formula Family Summary | A28:C36 | New section |
| Scope Notes | A38:F44 | New section |
| Portfolio Framing | A46:F50 | New section |
| Data Source | A52:F55 | New section |
| Workbook Health Check | F2 | Optional error-check formula |

### Title Banner (A1:F1)
Text: `"Rate Shock Loan Affordability & Refinance Dashboard"` — dark navy fill, white 16pt bold Calibri, center-aligned.

### Context Block (A3:F4)
Three labeled fields:
- `Primary Tool:` Microsoft Excel
- `Primary Dataset:` FRED / Freddie Mac 30-Year Fixed Mortgage Rate Series (MORTGAGE30US)
- `Project Type:` Consumer Finance · Financial Modeling · Decision Analytics

### Project Objective (A6:F8)
**A6** — Section header: `"Project Objective"` — Section Header style

Text (A7:F8 merged): *"This workbook analyzes how changes in mortgage interest rates affect borrower affordability, monthly payments, lifetime interest burden, and refinance decisions. Using historical U.S. 30-year fixed mortgage rate data (FRED/Freddie Mac, 1971–2026), the model converts macro-rate conditions into practical household finance scenarios and decision-support outputs."*

### Business Questions Table (A10:E16)

**A9** — Section header: `"Business Questions"` — Section Header style

Column headers (row 10): `ID` | `Business Question` | `Why It Matters` | `Primary Answer Location`

| Row | ID | Business Question | Why It Matters | Answer Location |
|---|---|---|---|---|
| 11 | BQ1 | How have 30-year fixed mortgage rates changed historically, and where does the latest rate sit relative to the past? | Establishes the rate environment that drives the model | Rate_Data; Dashboard |
| 12 | BQ2 | How much does a mortgage rate increase or decrease change the monthly payment on a representative loan? | Measures payment sensitivity to rate shocks | Rate_Shock_Model; Dashboard |
| 13 | BQ3 | How does the interest-rate environment affect total interest paid over the full life of the loan? | Shows the long-term cost of rate changes | Rate_Shock_Model; Amortization_Schedule; Dashboard |
| 14 | BQ4 | Given a fixed monthly payment budget, how much loan principal is affordable at different mortgage rates? | Quantifies purchasing-power erosion when rates rise | Rate_Shock_Model; Dashboard |
| 15 | BQ5 | Under what conditions does refinancing become financially attractive, and how long does it take to break even? | Supports a practical refinance decision | Refinance_Analysis; Dashboard |

### Workbook Navigation Map (A18:E26)

**A17** — Section header: `"Workbook Navigation Map"` — Section Header style

Column headers (row 18): `Worksheet` | `Role in the Model` | `Key Outputs` | `BQ Answered`

| Row | Worksheet | Role | Key Outputs | BQ |
|---|---|---|---|---|
| 19 | Project_Map | Executive overview & navigation | Objective, BQ list, sheet map, scope, portfolio framing | — |
| 20 | Rate_Data | Imported rate dataset & historical preparation | Latest rate, min/max/avg, helper columns, summary stats | BQ1 |
| 21 | Assumptions | Central input & scenario-control panel | Loan inputs, refinance inputs, named ranges | All |
| 22 | Rate_Shock_Model | Interest-rate scenario analysis | 6-scenario payment, interest, cost, affordability table | BQ2–BQ4 |
| 23 | Amortization_Schedule | Base-case loan mechanics over time | 360-row P&I schedule, balance decline, totals | BQ2–BQ3 |
| 24 | Refinance_Analysis | Refinance comparison & break-even model | Monthly savings, break-even months, decision flag | BQ5 |
| 25 | Dashboard | Executive visual summary | 5 KPI cards, 4 charts, refinance summary | All |

### Formula Family Summary (A28:C36)

**A27** — Section header: `"Core Formula Families"` — Section Header style

Column headers (row 28): `Formula / Logic` | `Project Use`

| Row | Formula | Project Use |
|---|---|---|
| 29 | PMT | Monthly mortgage payment under each rate scenario and base case |
| 30 | PV | Affordable loan principal at a fixed target monthly payment |
| 31 | CUMIPMT | Total interest paid over the full loan term by scenario |
| 32 | PPMT / IPMT | Principal and interest components by period in amortization |
| 33 | IF | Decision flags, refinance break-even interpretation, error guards |
| 34 | IFERROR | Graceful handling of division-by-zero and invalid inputs |
| 35 | INDEX / MATCH | Latest-rate retrieval from Rate_Data; robust to data appends |
| 36 | TEXT / YEAR | Date helper columns and formatted display labels |

### Scope Notes (A38:F44)

**A37** — Section header: `"Scope — Version 1"` — Section Header style

**Included in Version 1** (A39:C42):
- Historical 30-year fixed rate analysis (FRED/Freddie Mac, 1971–2026)
- Six-scenario rate shock analysis (−1.00% to +1.50%)
- Full 360-payment base-case amortization schedule
- Refinance break-even analysis with decision flag
- Executive dashboard with 5 KPI cards and 4 charts

**Excluded from Version 1** (D39:F42):
- Property taxes, homeowners insurance, PMI, HOA fees
- Adjustable-rate mortgage (ARM) modeling
- Credit-score-based loan pricing
- Regional housing-price differences
- VBA macros or automated data refresh
- AWS pipeline (planned for a later phase)

### Portfolio Framing Statement (A46:F50)

**A45** — Section header: `"Portfolio Framing"` — Section Header style

Text (A46:F50 merged): *"This project demonstrates applied financial modeling in Excel using real-world macroeconomic data. It translates a publicly available interest-rate series into actionable household finance scenarios — quantifying how rate movements affect monthly payments, lifetime borrowing costs, purchasing power, and refinance decisions. The workbook is structured as a decision-support tool, not a calculator: every output is tied to a specific business question, and the Dashboard is designed for a non-technical stakeholder audience. A later phase will extend this project into an AWS-aligned portfolio piece, with the mortgage-rate CSV stored in Amazon S3, queried via Athena, and summarized with SQL — keeping the Excel model as the analytical and visualization layer."*

### Data Source (A52:F55)

**A51** — Section header: `"Data Source"` — Section Header style

| Field | Value |
|---|---|
| Source | Federal Reserve Bank of St. Louis (FRED) — Freddie Mac Primary Mortgage Market Survey |
| Series | MORTGAGE30US — 30-Year Fixed Rate Mortgage Average in the United States |
| Frequency | Weekly (Thursday) |
| Date Range | April 2, 1971 through May 14, 2026 (~2,877 observations) |
| Units | Percent, not seasonally adjusted |
| URL | https://fred.stlouisfed.org/series/MORTGAGE30US |

### Workbook Health Check (F2)
Optional formula cell — displays a workbook-level status indicator:
```excel
=IF(
  ISERROR(Rate_Shock_Model!D13)+
  ISERROR(Rate_Shock_Model!F13)+
  ISERROR(Refinance_Analysis!B16)+
  ISERROR(Amortization_Schedule!I5)>0,
  "⚠ Errors Detected — Check Model Sheets",
  "✓ All Key Cells OK"
)
```
Format: bold, conditional formatting — green fill for "✓", red fill for "⚠".

---

## 10. Formula Safeguards and Error Handling

### IFERROR Patterns

| Formula Type | Safeguard Pattern | Reason |
|---|---|---|
| PMT | `=IFERROR(-PMT(rate/12, nper, -pv), "—")` | nper = 0 would error; rate = 0 is valid |
| CUMIPMT | `=IFERROR(-CUMIPMT(rate/12, nper, pv, 1, nper, 0), "—")` | rate = 0 causes #NUM! |
| PV | `=IFERROR(-PV(rate/12, nper, pmt), "—")` | nper = 0 would error |
| PPMT / IPMT | `=IFERROR(-PPMT(...), 0)` | Use 0 fallback (used in balance calculation) |
| Break-even division | `=IF(OR(NOT(ISNUMBER(savings)), savings<=0), "No Savings...", costs/savings)` | Explicit guard before division |
| INDEX/MATCH | `=IFERROR(INDEX(...,MATCH(...)), "—")` | Protects against no-match |

### Zero-Rate Edge Case
If `Assump_BaseRate` is 0, PMT and PV compute correctly (payment = loan/nper), but CUMIPMT returns #NUM!. The IFERROR wrapper handles this gracefully with "—".

### Circular Reference Prevention
- Amortization row 10 Beginning Balance uses `=Assump_LoanAmt` (not a prior-row reference). This is the chain-breaker.
- No sheet references itself.

### Data Validation Summary

| Sheet | Cell | Rule | Error Message |
|---|---|---|---|
| Assumptions | B5 | Decimal 0–1 | "Enter as decimal between 0 and 1" |
| Assumptions | B15 | Decimal 0.001–0.30 | "Enter rate as decimal e.g. 0.07" |
| Assumptions | B16 | Decimal 0.001–0.30 | "Enter rate as decimal e.g. 0.06" |
| Assumptions | B17 | Whole number 1–360 | "Enter months remaining 1–360" |
| Assumptions | B19 | Whole number 1–360 | "Enter holding period 1–360 months" |

> B7 (Loan Term) has no validation — it is a Formula Cell (`=30`), not an Input Cell.

---

## 11. QA Checks

> **Important**: All numeric benchmarks below are **default-assumptions tests only**. They are valid when Assumptions holds exactly: Home Price $500,000, Down Payment 20%, Loan Amount $400,000, Term 360 months, Base Rate 6.36%, Budget $2,500, Refi: Balance $380,000, Current Rate 7.00%, Proposed Rate 6.00%, Remaining Term 324 months, Closing Costs $6,000, Holding Period 60 months.
>
> The **formula-consistency checks** (PASS/FAIL cells in Amortization_Schedule I6 and I8) remain authoritative under any valid inputs and should always show `✓ PASS`.

### Rate_Data QA

| Check | How to Verify | Expected (default data) |
|---|---|---|
| Latest Rate | H2 value | `6.36` (matches 2026-05-14 row in column B) |
| Historical Max | H4 value | `18.63` |
| Historical Min | H3 value | `~2.65` |
| Row count | `=ROWS(tblMortgageRates)` | ~2,877 (verify matches actual last row) |
| Rate_Decimal formula | C2 = B2/100 | `0.0733` for row 2 (1971-04-02, rate 7.33) |
| CurrentRateLine | F2 = H2 | Same value as H2 |

> **Note**: The Rate_Decimal check uses row 2 (the first data row, 1971-04-02, rate 7.33), not the latest-rate row. The latest rate is in the last data row, not row 2.

### Assumptions QA *(default-assumptions test only)*

| Check | Cell | Expected Value |
|---|---|---|
| Loan Amount | B6 | `$400,000` (= $500,000 × 80%) |
| Term (months) | B8 | `360` (= 30 × 12) |
| Base Rate | B9 | `~6.36%` (linked from Rate_Data H2 ÷ 100) |

### Rate_Shock_Model QA *(default-assumptions test only)*

| Check | Cell | Expected Value | Tolerance |
|---|---|---|---|
| Base Case Monthly Payment | D13 | ~$2,491.56 | ±$1 |
| Base Case Total Interest | F13 | ~$496,960 | ±$500 |
| Base Case Total Loan Cost | G13 | ~$896,960 | ±$500 |
| Base Case Affordable Principal | H13 | ~$401,356 | ±$500 |
| −1% scenario payment (5.36%) | D11 | ~$2,236 | ±$5 |
| +1.5% scenario payment (7.86%) | D16 | ~$2,896 | ±$5 |
| Payment delta Base Case | E13 | `$0.00` | Exactly zero (formula-consistency check) |
| No "—" displayed | D11:H16 | All numeric | Under default inputs |

> **Derivation**: Loan Amount = $500,000 × (1 − 0.20) = **$400,000**. PMT(6.36%/12, 360, −400000) ≈ $2,491.56. All benchmarks use this $400,000 loan amount. Values confirmed against the authoritative workbook (May 2026).

### Amortization_Schedule QA

| Check | Cell | Expected (default) | Type |
|---|---|---|---|
| Month 1 Beginning Balance | B10 | `$400,000.00` | Default-assumptions test |
| Month 1 Interest Paid | E10 | ~$2,120 | Default-assumptions test (= $400,000 × 6.36%/12) |
| Month 1 Principal Paid | D10 | ~$377 | Default-assumptions test (= payment − interest) |
| Month 360 Ending Balance | F369 | < $1.00 | **Formula-consistency check** (always valid) |
| Total Principal = Loan Amount | I6 | `✓ PASS` | **Formula-consistency check** (always valid) |
| Balance ≈ $0 | I8 | `✓ PASS` | **Formula-consistency check** (always valid) |
| Total Interest | I5 | ~$499,000 | Default-assumptions test |

### Refinance_Analysis QA *(default-assumptions test only)*

| Check | Cell | Expected Value | Tolerance |
|---|---|---|---|
| Current Payment | B13 | ~$2,613.70 | ±$5 (PMT at 7.00%, 324 mo, $380K) |
| New Payment | B14 | ~$2,371.14 | ±$5 (PMT at 6.00%, 324 mo, $380K) |
| Monthly Savings | B15 | ~$242.55 | ±$5 |
| Break-Even Months | B16 | ~24.7 | ±0.5 (= $6,000 ÷ $242.55) |
| Decision Flag | B22 | `"Refinance Makes Sense"` | 24.7 < 60 months |

### Dashboard QA

| Check | Expected |
|---|---|
| KPI Card 1 (Latest Rate) | `"6.36%"` (default-assumptions test) |
| KPI Card 2 (Monthly Payment) | ~`$2,491.56` (default-assumptions test) |
| KPI Card 3 (Total Interest) | ~`$496,960` (default-assumptions test) |
| KPI Card 4 (Affordable Loan) | ~`$401,356` (default-assumptions test) |
| KPI Card 5 (Break-Even) | `"24.7 months"` (default-assumptions test) |
| No Input Cells on Dashboard | No yellow-filled cells visible |
| All 4 charts present | Charts visible and populated |

---

## 12. Preservation Notes

### What to Keep (Do Not Modify)

| Sheet | What to Preserve |
|---|---|
| Rate_Data | All values in columns A (`observation_date`) and B (`MORTGAGE30US`) — full contiguous data range (~2,877 rows as of May 2026) |
| Rate_Data | Column headers in row 1 |
| Project_Map | Title banner — polish style to match workbook palette if needed |
| Project_Map | Dataset/context block — verify accuracy, do not delete |
| Project_Map | Objective section — verify completeness, do not delete |
| Project_Map | Business question table headers — complete the rows per Section 9 |

### What to Polish or Complete

| Sheet | Action |
|---|---|
| Rate_Data | Convert full contiguous A:B range to Excel Table named `tblMortgageRates` |
| Rate_Data | Add helper columns C (Rate_Decimal), D (Year), E (Month_Year), F (CurrentRateLine) |
| Rate_Data | Add summary block G1:H8 |
| Rate_Data | Apply column header formatting to row 1 |
| Project_Map | Complete business questions table rows (BQ1–BQ5) |
| Project_Map | Add Workbook Navigation Map table |
| Project_Map | Add Formula Family Summary table |
| Project_Map | Add Scope Notes section |
| Project_Map | Add Portfolio Framing statement |
| Project_Map | Add Data Source section |
| Project_Map | Add optional Workbook Health Check formula (F2) |
| All sheets | Apply consistent Calibri font, color palette, and number formats per Section 2 |
| All sheets | Set column widths per each sheet's specification |
| All sheets | Apply freeze panes where specified |

### What to Build from Scratch

| Sheet | Status |
|---|---|
| Assumptions | Empty — build entirely per Section 4 |
| Rate_Shock_Model | Empty — build entirely per Section 5 |
| Amortization_Schedule | Empty — build entirely per Section 6 |
| Refinance_Analysis | Empty — build entirely per Section 7 |
| Dashboard | Empty — build entirely per Section 8 |

---

## 13. Implementation Order

Build sheets in this sequence to ensure all dependencies are available:

1. **Rate_Data** — Convert to table, add helper columns and summary block
2. **Assumptions** — Add all input/formula cells; define all named ranges
3. **Rate_Shock_Model** — Build scenario table (depends on Assumptions)
4. **Amortization_Schedule** — Build 360-row schedule (depends on Assumptions)
5. **Refinance_Analysis** — Build break-even model (depends on Assumptions)
6. **Dashboard** — Build KPI cards and insert charts (depends on all model sheets)
7. **Project_Map** — Complete all documentation sections (no formula dependencies)

---

## 14. Named Range Master List

| Name | Refers To | Used In |
|---|---|---|
| `LatestRate` | `=Rate_Data!$H$2` | Assumptions B9 |
| `Assump_HomePrice` | `=Assumptions!$B$4` | — |
| `Assump_DPPct` | `=Assumptions!$B$5` | — |
| `Assump_LoanAmt` | `=Assumptions!$B$6` | Rate_Shock_Model, Amortization_Schedule |
| `Assump_TermYrs` | `=Assumptions!$B$7` | — |
| `Assump_TermMos` | `=Assumptions!$B$8` | Rate_Shock_Model, Amortization_Schedule |
| `Assump_BaseRate` | `=Assumptions!$B$9` | Rate_Shock_Model, Amortization_Schedule |
| `Assump_Budget` | `=Assumptions!$B$10` | Rate_Shock_Model |
| `Assump_RemBal` | `=Assumptions!$B$14` | Refinance_Analysis |
| `Assump_CurrRate` | `=Assumptions!$B$15` | Refinance_Analysis, Dashboard KPI 5 |
| `Assump_PropRate` | `=Assumptions!$B$16` | Refinance_Analysis, Dashboard KPI 5 |
| `Assump_RemTerm` | `=Assumptions!$B$17` | Refinance_Analysis |
| `Assump_ClosingCosts` | `=Assumptions!$B$18` | Refinance_Analysis, Dashboard KPI 5 |
| `Assump_HoldPeriod` | `=Assumptions!$B$19` | Refinance_Analysis |
| `RSM_BasePayment` | `=Rate_Shock_Model!$D$13` | Dashboard KPI 2 |
| `RSM_BaseTotalInterest` | `=Rate_Shock_Model!$F$13` | Dashboard KPI 3 |
| `RSM_BaseAffordable` | `=Rate_Shock_Model!$H$13` | Dashboard KPI 4 |
| `RSM_Labels` | `=Rate_Shock_Model!$A$11:$A$16` | Dashboard charts 2–4 |
| `RSM_Payments` | `=Rate_Shock_Model!$D$11:$D$16` | Dashboard chart 2 |
| `RSM_TotalInterest` | `=Rate_Shock_Model!$F$11:$F$16` | Dashboard chart 3 |
| `RSM_Affordable` | `=Rate_Shock_Model!$H$11:$H$16` | Dashboard chart 4 |
| `Refi_CurrPayment` | `=Refinance_Analysis!$B$13` | Dashboard refi table |
| `Refi_NewPayment` | `=Refinance_Analysis!$B$14` | Dashboard refi table |
| `Refi_MonthlySavings` | `=Refinance_Analysis!$B$15` | Dashboard refi table |
| `Refi_BreakEvenMos` | `=Refinance_Analysis!$B$16` | Dashboard KPI 5, refi table |
| `Refi_DecisionFlag` | `=Refinance_Analysis!$B$22` | Dashboard KPI 5, refi table |
| `Amort_TotalPayments` | `=Amortization_Schedule!$I$3` | Dashboard (optional) |
| `Amort_TotalPrincipal` | `=Amortization_Schedule!$I$4` | Dashboard (optional) |
| `Amort_TotalInterest` | `=Amortization_Schedule!$I$5` | Dashboard (optional) |

---

## 15. Correctness Properties

Key invariants that must hold under any valid input:

**Property 1 — Loan Fully Amortizes**: `Amortization_Schedule!F369` < $1.00. Validates Req 4.7, 4.9.

**Property 2 — Principal Conservation**: `SUM(D10:D369)` ≈ `Assump_LoanAmt` within $1.00. Validates Req 4.9.

**Property 3 — Base Case Delta Is Zero**: `Rate_Shock_Model!E13` = $0.00 exactly. Validates Req 3.4.

**Property 4 — Payment Monotonicity**: D11 < D12 < D13 < D14 < D15 < D16 in Rate_Shock_Model. Validates Req 3.3.

**Property 5 — Affordability Inverse Monotonicity**: H11 > H12 > H13 > H14 > H15 > H16 in Rate_Shock_Model. Validates Req 3.7.

**Property 6 — Savings Correctness**: `Refinance_Analysis!B15` = B13 − B14. Validates Req 5.3.

**Property 7 — No Visible Errors**: No #DIV/0!, #REF!, #VALUE!, or #NAME? in any visible cell under default inputs. Validates Req 8.7.
