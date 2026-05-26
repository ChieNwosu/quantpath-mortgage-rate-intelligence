# Implementation Plan: Rate Shock Loan Affordability & Refinance Dashboard

## Overview

Build out the Rate Shock Loan Affordability & Refinance Dashboard workbook from the existing skeleton (Rate_Data populated, Project_Map partially started) into a complete, portfolio-ready financial analytics product. All work is additive — no existing data or sheet names are modified. Implementation follows the sheet dependency order: Rate_Data → Assumptions → Rate_Shock_Model → Amortization_Schedule → Refinance_Analysis → Dashboard → Project_Map.

---

## Tasks

- [x] 1. Workbook preparation and file safety
  - [x] 1.1 Create a working backup copy of the workbook
    - Copy `Rate Shock Loan Affordability & Refinance Dashboard.xlsx` to a `_backup` filename in the same folder before making any changes
    - Confirm the original file is closed before editing to avoid co-authoring conflicts
    - _Requirements: 8.1_

  - [x] 1.2 Verify sheet inventory and tab order
    - Open the workbook and confirm exactly seven sheets exist: `Project_Map`, `Rate_Data`, `Assumptions`, `Rate_Shock_Model`, `Amortization_Schedule`, `Refinance_Analysis`, `Dashboard`
    - Confirm tab order matches the spec (left to right): Project_Map → Rate_Data → Assumptions → Rate_Shock_Model → Amortization_Schedule → Refinance_Analysis → Dashboard
    - If any sheet is missing, insert it with the exact name specified; do not rename existing sheets
    - _Requirements: 8.1_

  - [x]* 1.3 Verify no circular references exist at baseline
    - Open Formulas → Error Checking → Circular References and confirm the list is empty before any new formulas are added
    - _Requirements: 8.6_

- [x] 2. Rate_Data table conversion and helper columns
  - [ ] 2.1 Convert the full contiguous Rate_Data source range to Excel Table named `tblMortgageRates`
    - On the Rate_Data sheet, click any cell in column A or B, then press Ctrl+Shift+End to confirm the last occupied row (currently row 2878, but may grow as data is appended)
    - Select from A1 down to the last occupied row in column B — the full contiguous range including the header row
    - Insert → Table → confirm "My table has headers" → OK
    - In the Table Design tab, rename the table to `tblMortgageRates`
    - Verify column A header remains `observation_date` and column B remains `MORTGAGE30US` — do not rename either
    - _Requirements: 1.1, 1.6_

  - [x] 2.2 Add helper column C — `Rate_Decimal`
    - Click the first empty cell in column C of the table (C2 in the data area)
    - Set the column header to `Rate_Decimal`
    - Enter formula: `=[@MORTGAGE30US]/100`
    - Apply number format `0.0000` to the column
    - Confirm the table auto-fills the formula down all data rows (currently ~2,877 rows)
    - _Requirements: 1.2_

  - [x] 2.3 Add helper column D — `Year`
    - Set column D header to `Year`
    - Enter formula in D2: `=YEAR([@observation_date])`
    - Apply format `General`
    - _Requirements: 1.3_

  - [x] 2.4 Add helper column E — `Month_Year`
    - Set column E header to `Month_Year`
    - Enter formula in E2: `=TEXT([@observation_date],"MMM-YYYY")`
    - Apply format `General`
    - _Requirements: 1.4_

  - [x] 2.5 Add helper column F — `CurrentRateLine`
    - Set column F header to `CurrentRateLine`
    - Enter formula in F2: `=Rate_Data!$H$2`
    - Apply format `0.00`
    - This column holds the same value in every row (latest rate), creating a flat horizontal reference line for the Dashboard chart
    - _Requirements: 1.5, 6.2_

  - [x] 2.6 Build summary block G1:H8
    - G1: type `"Rate Summary Statistics"` — apply Section Header style (medium blue fill `#2E75B6`, white bold 12pt)
    - G2: `Latest Rate` | H2: `=INDEX(tblMortgageRates[MORTGAGE30US],MATCH(MAX(tblMortgageRates[observation_date]),tblMortgageRates[observation_date],0))` | format `0.00`
    - G3: `Historical Minimum` | H3: `=MIN(tblMortgageRates[MORTGAGE30US])` | format `0.00`
    - G4: `Historical Maximum` | H4: `=MAX(tblMortgageRates[MORTGAGE30US])` | format `0.00`
    - G5: `Historical Average` | H5: `=AVERAGE(tblMortgageRates[MORTGAGE30US])` | format `0.00`
    - G6: `52-Week Trailing Avg` | H6: `=AVERAGE(OFFSET(INDEX(tblMortgageRates[MORTGAGE30US],ROWS(tblMortgageRates[MORTGAGE30US])),0,0,-52,1))` | format `0.00`
    - G7: `Data Start` | H7: `=TEXT(MIN(tblMortgageRates[observation_date]),"MMM D, YYYY")` | format `General`
    - G8: `Data End` | H8: `=TEXT(MAX(tblMortgageRates[observation_date]),"MMM D, YYYY")` | format `General`
    - _Requirements: 1.5, 1.7_

  - [x] 2.7 Define named range `LatestRate`
    - Open Formulas → Name Manager → New
    - Name: `LatestRate` | Refers To: `=Rate_Data!$H$2` | Scope: Workbook
    - _Requirements: 1.5, 2.3_

  - [x] 2.8 Apply Rate_Data formatting and freeze panes
    - Apply column header row style (light blue fill `#D6E4F0`, dark navy font `#1F3864`, bold 11pt) to row 1 of the table
    - Freeze row 1 (View → Freeze Panes → Freeze Top Row)
    - _Requirements: 8.2, 8.3_

  - [x]* 2.9 Verify Rate_Data QA checks
    - Confirm H2 = `6.36` (or matches the latest row in column B)
    - Confirm H4 = `18.63` (historical max)
    - Confirm C2 = B2/100 (e.g., `0.0733` for the 1971-04-02 row)
    - Confirm F2 = H2 (CurrentRateLine equals Latest Rate)
    - Confirm `=ROWS(tblMortgageRates)` returns ~2,877 (verify matches actual last row in the workbook)
    - _Requirements: 1.1–1.6_

- [x] 3. Assumptions tab build
  - [x] 3.1 Build sheet title banner (A1:D1)
    - Merge A1:D1
    - Type: `"Assumptions — Input Control Panel"`
    - Apply title banner style: dark navy fill `#1F3864`, white 14pt bold Calibri, center-aligned
    - _Requirements: 2.5, 8.2_

  - [x] 3.2 Build Loan Inputs section (A3:D10)
    - A3: `"LOAN INPUTS"` — Section Header style (medium blue fill, white bold 12pt)
    - Row 4: A4=`Home Price`, B4=`500000` (Input Cell — yellow fill `#FFF2CC`), D4=`"Enter purchase price"` | format B4: `$#,##0`
    - Row 5: A5=`Down Payment %`, B5=`0.20` (Input Cell), D5=`"Enter as decimal (e.g., 0.20)"` | format B5: `0.00%`
    - Row 6: A6=`Loan Amount`, B6=`=B4*(1-B5)` (Formula Cell — white fill, locked), D6=`"Computed: Home Price × (1 − DP%)"` | format B6: `$#,##0`
    - Row 7: A7=`Loan Term (years)`, B7=`=30` (Formula Cell — white fill, locked), D7=`"Fixed: 30-yr fixed rate model"` | format B7: `#,##0`
    - Row 8: A8=`Loan Term (months)`, B8=`=B7*12` (Formula Cell), D8=`"Computed: 360 months"` | format B8: `#,##0`
    - Row 9: A9=`Base Mortgage Rate`, B9=`=LatestRate/100` (Formula Cell), D9=`"Auto-linked from Rate_Data"` | format B9: `0.00%`
    - Row 10: A10=`Target Monthly Budget`, B10=`2500` (Input Cell), D10=`"Max monthly P&I payment"` | format B10: `$#,##0`
    - Set column widths: A=28, B=18, C=2 (spacer), D=35
    - _Requirements: 2.1, 2.2, 2.3, 2.6_

  - [x] 3.3 Build Refinance Inputs section (A13:D20)
    - A13: `"REFINANCE INPUTS"` — Section Header style
    - Row 14: A14=`Remaining Balance`, B14=`380000` (Input Cell) | format: `$#,##0`
    - Row 15: A15=`Current Mortgage Rate`, B15=`0.07` (Input Cell) | format: `0.00%`
    - Row 16: A16=`Proposed Refinance Rate`, B16=`0.06` (Input Cell) | format: `0.00%`
    - Row 17: A17=`Remaining Term (months)`, B17=`324` (Input Cell) | format: `#,##0`
    - Row 18: A18=`Closing Costs`, B18=`6000` (Input Cell) | format: `$#,##0`
    - Row 19: A19=`Expected Holding Period`, B19=`60` (Input Cell) | format: `#,##0`
    - _Requirements: 2.4, 2.5, 2.6_

  - [x] 3.4 Apply data validation to Assumptions input cells
    - B5 (Down Payment %): Decimal, between 0 and 1. Error alert: `"Enter as decimal between 0 and 1 (e.g., 0.20 for 20%)."`
    - B15 (Current Mortgage Rate): Decimal, between 0.001 and 0.30. Error alert: `"Enter rate as decimal (e.g., 0.07 for 7%)."`
    - B16 (Proposed Refinance Rate): Decimal, between 0.001 and 0.30. Error alert: `"Enter rate as decimal (e.g., 0.06 for 6%)."`
    - B17 (Remaining Term): Whole number, between 1 and 360. Error alert: `"Enter months remaining 1–360."`
    - B19 (Expected Holding Period): Whole number, between 1 and 360. Error alert: `"Enter holding period 1–360 months."`
    - Do NOT add validation to B7 — it is a Formula Cell, not an Input Cell
    - _Requirements: 2.7, 2.8_

  - [x] 3.5 Define all 13 Assump_* named ranges
    - Open Formulas → Name Manager and create each at Workbook scope:
    - `Assump_HomePrice` = `Assumptions!$B$4`
    - `Assump_DPPct` = `Assumptions!$B$5`
    - `Assump_LoanAmt` = `Assumptions!$B$6`
    - `Assump_TermYrs` = `Assumptions!$B$7`
    - `Assump_TermMos` = `Assumptions!$B$8`
    - `Assump_BaseRate` = `Assumptions!$B$9`
    - `Assump_Budget` = `Assumptions!$B$10`
    - `Assump_RemBal` = `Assumptions!$B$14`
    - `Assump_CurrRate` = `Assumptions!$B$15`
    - `Assump_PropRate` = `Assumptions!$B$16`
    - `Assump_RemTerm` = `Assumptions!$B$17`
    - `Assump_ClosingCosts` = `Assumptions!$B$18`
    - `Assump_HoldPeriod` = `Assumptions!$B$19`
    - _Requirements: 8.5_

  - [x]* 3.6 Verify Assumptions QA checks (default inputs)
    - Confirm B6 = `$400,000` (= $500,000 × 80%)
    - Confirm B8 = `360` (= 30 × 12)
    - Confirm B9 ≈ `6.36%` (linked from Rate_Data H2 ÷ 100)
    - _Requirements: 2.1–2.4_

- [x] 4. Rate_Shock_Model build
  - Depends on: Phase 3 complete (all Assump_* named ranges defined)

  - [x] 4.1 Build sheet title banner and input reference block
    - Merge A1:H1; type `"Rate Shock Scenario Analysis"`; apply title banner style (dark navy fill, white 14pt bold)
    - A2: `"Key Inputs (from Assumptions)"` — Section Header style
    - Row 3: A3=`Loan Amount`, B3=`=Assump_LoanAmt` | format: `$#,##0`
    - Row 4: A4=`Base Rate`, B4=`=Assump_BaseRate` | format: `0.00%`
    - Row 5: A5=`Loan Term (months)`, B5=`=Assump_TermMos` | format: `#,##0`
    - Row 6: A6=`Monthly Budget`, B6=`=Assump_Budget` | format: `$#,##0`
    - _Requirements: 3.1, 8.5_

  - [x] 4.2 Build scenario table column headers (row 10)
    - Apply column header row style (light blue fill `#D6E4F0`, dark navy font, bold 11pt) to A10:H10
    - A10=`Scenario`, B10=`Rate Offset`, C10=`Scenario Rate`, D10=`Monthly Payment`
    - E10=`Δ Payment vs. Base`, F10=`Total Lifetime Interest`, G10=`Total Loan Cost`, H10=`Affordable Principal`
    - Set column widths: A=16, B=12, C=14, D=16, E=20, F=22, G=18, H=20
    - _Requirements: 3.1_

  - [x] 4.3 Enter scenario labels and rate offsets (A11:B16)
    - A11=`Rate −1.00%`, A12=`Rate −0.50%`, A13=`Base Case`, A14=`Rate +0.50%`, A15=`Rate +1.00%`, A16=`Rate +1.50%`
    - B11=`-0.01`, B12=`-0.005`, B13=`0`, B14=`0.005`, B15=`0.01`, B16=`0.015`
    - Format B11:B16 as `0.00%`
    - _Requirements: 3.1_

  - [x] 4.4 Enter Scenario Rate formulas (column C)
    - C11: `=Assump_BaseRate+B11` — copy down through C16
    - Format C11:C16 as `0.00%`
    - _Requirements: 3.2_

  - [x] 4.5 Enter Monthly Payment formulas (column D)
    - D11: `=IFERROR(PMT(C11/12, Assump_TermMos, -Assump_LoanAmt), "—")` — copy down through D16
    - Format D11:D16 as `$#,##0.00`
    - _Requirements: 3.3_

  - [x] 4.6 Enter Payment Change vs. Base formulas (column E)
    - E11: `=IFERROR(D11-$D$13, "—")` — copy down through E16
    - E13: enter `=0` explicitly (Base Case delta is exactly zero)
    - Format E11:E16 as `$#,##0.00`
    - Apply conditional formatting to E11:E16: negative values → green font `#375623`; positive values → red font `#C00000`
    - _Requirements: 3.4_

  - [x] 4.7 Enter Total Lifetime Interest formulas (column F)
    - F11: `=IFERROR(-CUMIPMT(C11/12, Assump_TermMos, Assump_LoanAmt, 1, Assump_TermMos, 0), "—")` — copy down through F16
    - Format F11:F16 as `$#,##0`
    - _Requirements: 3.5_

  - [x] 4.8 Enter Total Loan Cost formulas (column G)
    - G11: `=IFERROR(Assump_LoanAmt+F11, "—")` — copy down through G16
    - Format G11:G16 as `$#,##0`
    - _Requirements: 3.6_

  - [x] 4.9 Enter Affordable Principal formulas (column H)
    - H11: `=IFERROR(-PV(C11/12, Assump_TermMos, Assump_Budget), "—")` — copy down through H16
    - Format H11:H16 as `$#,##0`
    - _Requirements: 3.7_

  - [x] 4.10 Apply Base Case row highlight and define RSM_* named ranges
    - Apply light green fill `#E2EFDA` and bold font to A13:H13
    - Define 7 named ranges at Workbook scope via Name Manager:
      - `RSM_BasePayment` = `Rate_Shock_Model!$D$13`
      - `RSM_BaseTotalInterest` = `Rate_Shock_Model!$F$13`
      - `RSM_BaseAffordable` = `Rate_Shock_Model!$H$13`
      - `RSM_Labels` = `Rate_Shock_Model!$A$11:$A$16`
      - `RSM_Payments` = `Rate_Shock_Model!$D$11:$D$16`
      - `RSM_TotalInterest` = `Rate_Shock_Model!$F$11:$F$16`
      - `RSM_Affordable` = `Rate_Shock_Model!$H$11:$H$16`
    - _Requirements: 3.9, 8.5_

  - [x]* 4.11 Verify Rate_Shock_Model QA checks (default inputs)
    - D13 ≈ `$2,491.56` (±$1) — Base Case monthly payment (confirmed against authoritative workbook)
    - F13 ≈ `$496,960` (±$500) — Base Case total interest
    - H13 ≈ `$401,356` (±$500) — Base Case affordable principal
    - E13 = `$0.00` exactly (formula-consistency check — Property 3)
    - D11 < D12 < D13 < D14 < D15 < D16 (payment monotonicity — Property 4)
    - H11 > H12 > H13 > H14 > H15 > H16 (affordability inverse monotonicity — Property 5)
    - No "—" displayed in D11:H16 under default inputs (Property 7)
    - _Requirements: 3.1–3.9_

- [x] 5. Amortization_Schedule build
  - Depends on: Phase 3 complete (all Assump_* named ranges defined)

  - [x] 5.1 Build sheet title banner, input reference block, and column headers
    - Merge A1:F1; type `"Base-Case Loan Amortization Schedule"`; apply title banner style
    - A2: `"Loan Parameters"` — Section Header style
    - Row 3: A3=`Loan Amount`, B3=`=Assump_LoanAmt` | format: `$#,##0`
    - Row 4: A4=`Annual Rate`, B4=`=Assump_BaseRate` | format: `0.00%`
    - Row 5: A5=`Monthly Rate`, B5=`=Assump_BaseRate/12` | format: `0.0000%`
    - Row 6: A6=`Term (months)`, B6=`=Assump_TermMos` | format: `#,##0`
    - Apply column header style to A9:F9; enter headers: A9=`Payment #`, B9=`Beginning Balance`, C9=`Monthly Payment`, D9=`Principal Paid`, E9=`Interest Paid`, F9=`Ending Balance`
    - Set column widths: A=12, B=18, C=18, D=16, E=16, F=18, G=4 (spacer), H=28, I=18
    - _Requirements: 4.1, 4.2_

  - [x] 5.2 Enter seed row formulas (row 10 — Month 1)
    - A10: `=1`
    - B10: `=Assump_LoanAmt`
    - C10: `=IFERROR(-PMT(Assump_BaseRate/12, Assump_TermMos, -Assump_LoanAmt), 0)`
    - D10: `=IFERROR(-PPMT(Assump_BaseRate/12, A10, Assump_TermMos, -Assump_LoanAmt), 0)`
    - E10: `=IFERROR(-IPMT(Assump_BaseRate/12, A10, Assump_TermMos, -Assump_LoanAmt), 0)`
    - F10: `=IFERROR(B10-D10, 0)`
    - Format B10:F10 as `$#,##0.00`
    - _Requirements: 4.3, 4.4, 4.5, 4.6, 4.7_

  - [x] 5.3 Enter row 11 repeating-pattern formulas and copy down to row 369
    - A11: `=A10+1`
    - B11: `=F10`
    - C11: `=IFERROR(-PMT(Assump_BaseRate/12, Assump_TermMos, -Assump_LoanAmt), 0)`
    - D11: `=IFERROR(-PPMT(Assump_BaseRate/12, A11, Assump_TermMos, -Assump_LoanAmt), 0)`
    - E11: `=IFERROR(-IPMT(Assump_BaseRate/12, A11, Assump_TermMos, -Assump_LoanAmt), 0)`
    - F11: `=IFERROR(B11-D11, 0)`
    - Select A11:F11 and copy down through row 369 (360 total data rows, Month 1 = row 10, Month 360 = row 369)
    - _Requirements: 4.1, 4.3, 4.4, 4.5, 4.6, 4.7, 4.8_

  - [x] 5.4 Build summary block (H2:I8) with PASS/FAIL checks
    - H2: `"Schedule Totals"` — Section Header style
    - H3=`Total Payments`, I3=`=SUM(C10:C369)` | format: `$#,##0.00`
    - H4=`Total Principal Paid`, I4=`=SUM(D10:D369)` | format: `$#,##0.00`
    - H5=`Total Interest Paid`, I5=`=SUM(E10:E369)` | format: `$#,##0.00`
    - H6=`Principal = Loan Amt?`, I6=`=IF(ABS(I4-Assump_LoanAmt)<1,"✓ PASS","✗ FAIL")` | format: `General`
    - H7=`Ending Balance (Mo 360)`, I7=`=F369` | format: `$#,##0.00`
    - H8=`Balance ≈ $0?`, I8=`=IF(ABS(F369)<1,"✓ PASS","✗ FAIL")` | format: `General`
    - _Requirements: 4.9_

  - [x] 5.5 Define Amort_* named ranges and apply freeze panes
    - Define 3 named ranges at Workbook scope:
      - `Amort_TotalPayments` = `Amortization_Schedule!$I$3`
      - `Amort_TotalPrincipal` = `Amortization_Schedule!$I$4`
      - `Amort_TotalInterest` = `Amortization_Schedule!$I$5`
    - Freeze rows 1–9: select row 10, then View → Freeze Panes → Freeze Panes
    - _Requirements: 8.5_

  - [x]* 5.6 Verify Amortization_Schedule QA checks
    - I6 = `✓ PASS` (formula-consistency check — Property 2, always valid)
    - I8 = `✓ PASS` (formula-consistency check — Property 1, always valid)
    - F369 < $1.00 (loan fully amortizes — Property 1)
    - B10 = `$400,000.00` (default-assumptions test)
    - E10 ≈ `$2,120` (default-assumptions test: $400,000 × 6.36%/12)
    - I5 ≈ `$499,000` (default-assumptions test)
    - _Requirements: 4.1–4.9_

- [x] 6. Refinance_Analysis build
  - Depends on: Phase 3 complete (all Assump_* named ranges defined)

  - [x] 6.1 Build sheet title banner and input display block
    - Merge A1:D1; type `"Refinance Break-Even Analysis"`; apply title banner style
    - A2: `"Refinance Inputs (from Assumptions)"` — Section Header style
    - Row 3: A3=`Remaining Balance`, B3=`=Assump_RemBal` | format: `$#,##0`
    - Row 4: A4=`Current Mortgage Rate`, B4=`=Assump_CurrRate` | format: `0.00%`
    - Row 5: A5=`Proposed Refinance Rate`, B5=`=Assump_PropRate` | format: `0.00%`
    - Row 6: A6=`Remaining Term (months)`, B6=`=Assump_RemTerm` | format: `#,##0`
    - Row 7: A7=`Closing Costs`, B7=`=Assump_ClosingCosts` | format: `$#,##0`
    - Row 8: A8=`Expected Holding Period`, B8=`=Assump_HoldPeriod` | format: `#,##0`
    - Set column widths: A=30, B=22, C=20
    - _Requirements: 5.7_

  - [x] 6.2 Build results block (A12:B19)
    - A12: `"Refinance Analysis Results"` — Section Header style
    - B13: `=IFERROR(-PMT(Assump_CurrRate/12, Assump_RemTerm, -Assump_RemBal), "—")` | label A13=`Current Monthly Payment` | format: `$#,##0.00`
    - B14: `=IFERROR(-PMT(Assump_PropRate/12, Assump_RemTerm, -Assump_RemBal), "—")` | label A14=`New Monthly Payment` | format: `$#,##0.00`
    - B15: `=IFERROR(B13-B14, "—")` | label A15=`Monthly Savings` | format: `$#,##0.00`
    - B16 (Break-Even Months): enter the zero-savings guard formula:
      ```
      =IFERROR(IF(OR(NOT(ISNUMBER(B15)),B15<=0),"No Savings — Refinance Not Beneficial",Assump_ClosingCosts/B15),"Error")
      ```
      label A16=`Break-Even Months` | format: `General`
    - B17: `=Assump_HoldPeriod` | label A17=`Expected Holding Period` | format: `#,##0`
    - B18: `=IFERROR(IF(ISNUMBER(B15),B15*Assump_HoldPeriod,"—"),"—")` | label A18=`Total Savings Over Holding Period` | format: `$#,##0`
    - B19: `=IFERROR(IF(ISNUMBER(B18),B18-Assump_ClosingCosts,"—"),"—")` | label A19=`Net Benefit After Closing Costs` | format: `$#,##0`
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5_

  - [x] 6.3 Build decision flag (B22) with conditional formatting
    - A21: `"Decision"` — Section Header style, 14pt
    - Merge B22:C22; enter formula:
      ```
      =IF(OR(NOT(ISNUMBER(B15)),B15<=0),"No Savings — Refinance Not Beneficial",IF(ISNUMBER(B16),IF(B16<=Assump_HoldPeriod,"Refinance Makes Sense","Does Not Break Even in Holding Period"),"No Savings — Refinance Not Beneficial"))
      ```
    - Format B22: 14pt bold, center-aligned
    - Apply 3 conditional formatting rules to B22:
      1. Cell value = `"Refinance Makes Sense"` → Fill `#E2EFDA`, Font `#375623`, Bold
      2. Cell contains `"Does Not Break Even"` → Fill `#FFE0E0`, Font `#C00000`, Bold
      3. Cell contains `"No Savings"` → Fill `#FFE0E0`, Font `#C00000`, Bold
    - _Requirements: 5.5, 5.6_

  - [x] 6.4 Define Refi_* named ranges
    - Define 5 named ranges at Workbook scope:
      - `Refi_CurrPayment` = `Refinance_Analysis!$B$13`
      - `Refi_NewPayment` = `Refinance_Analysis!$B$14`
      - `Refi_MonthlySavings` = `Refinance_Analysis!$B$15`
      - `Refi_BreakEvenMos` = `Refinance_Analysis!$B$16`
      - `Refi_DecisionFlag` = `Refinance_Analysis!$B$22`
    - _Requirements: 8.5_

  - [x]* 6.5 Verify Refinance_Analysis QA checks (default inputs)
    - B13 ≈ `$2,661` (±$5) — current payment at 7.00%, 324 mo, $380K
    - B14 ≈ `$2,432` (±$5) — new payment at 6.00%, 324 mo, $380K
    - B15 ≈ `$229` (±$5) — monthly savings
    - B16 ≈ `26.2` (±0.5) — break-even months (= $6,000 ÷ $229)
    - B22 = `"Refinance Makes Sense"` (26.2 < 60 months)
    - Confirm B15 = B13 − B14 (Property 6)
    - _Requirements: 5.1–5.7_

- [x] 7. Dashboard build
  - Depends on: Phases 4, 5, and 6 complete (all RSM_*, Refi_*, Amort_* named ranges defined)

  - [x] 7.1 Build title banner (A1:P1)
    - Merge A1:P1; type `"Rate Shock Loan Affordability & Refinance Dashboard"`
    - Apply title banner style: dark navy fill `#1F3864`, white 16pt bold Calibri, center-aligned, row height 36pt
    - _Requirements: 6.9_

  - [x] 7.2 Build KPI Card 1 — Latest Mortgage Rate (A3:C10)
    - Fill A3:C10 with `#EBF3FB`
    - Merge A4:C4; type `"Latest 30-Yr Fixed Rate"` — dark navy, bold 11pt, center-aligned
    - Merge A5:C7; enter `=TEXT(Rate_Data!H2,"0.00")&"%"` — 28pt bold dark navy, center-aligned
    - Merge A8:C8; enter `="Source: FRED · "&TEXT(MAX(tblMortgageRates[observation_date]),"MMM D, YYYY")` — 9pt italic gray
    - _Requirements: 6.1, 6.7, 6.8_

  - [x] 7.3 Build KPI Card 2 — Base Case Monthly Payment (D3:F10)
    - Fill D3:F10 with `#EBF3FB`
    - Merge D4:F4; type `"Base Case Monthly P&I"` — dark navy, bold 11pt, center-aligned
    - Merge D5:F7; enter `=RSM_BasePayment` — format `$#,##0.00`, 28pt bold dark navy, center-aligned
    - Merge D8:F8; enter `="$"&TEXT(Assump_LoanAmt/1000,"#,##0")&"K loan · 30 yrs · "&TEXT(Assump_BaseRate,"0.00%")` — 9pt italic gray
    - _Requirements: 6.1, 6.7, 6.8_

  - [x] 7.4 Build KPI Card 3 — Total Lifetime Interest (G3:I10)
    - Fill G3:I10 with `#EBF3FB`
    - Merge G4:I4; type `"Total Lifetime Interest"` — dark navy, bold 11pt, center-aligned
    - Merge G5:I7; enter `=RSM_BaseTotalInterest` — format `$#,##0`, 28pt bold dark navy, center-aligned
    - Merge G8:I8; type `"Base case · 30-year term"` — 9pt italic gray
    - _Requirements: 6.1, 6.7, 6.8_

  - [x] 7.5 Build KPI Card 4 — Affordable Loan at Budget (J3:L10)
    - Fill J3:L10 with `#EBF3FB`
    - Merge J4:L4; enter `="Affordable Loan @ $"&TEXT(Assump_Budget/1000,"#,##0")&"K/mo"` — dark navy, bold 11pt, center-aligned
    - Merge J5:L7; enter `=RSM_BaseAffordable` — format `$#,##0`, 28pt bold dark navy, center-aligned
    - Merge J8:L8; type `"At base rate · 30-yr term"` — 9pt italic gray
    - _Requirements: 6.1, 6.7, 6.8_

  - [x] 7.6 Build KPI Card 5 — Refinance Break-Even (M3:P10)
    - Fill M3:P10 with `#EBF3FB`
    - Merge M4:P4; type `"Refi Break-Even"` — dark navy, bold 11pt, center-aligned
    - Merge M5:P7; enter `=IF(ISNUMBER(Refi_BreakEvenMos),TEXT(Refi_BreakEvenMos,"0.0")&" months","N/A")` — 28pt bold dark navy, center-aligned
    - Merge M8:P8; enter `=TEXT(Assump_CurrRate,"0.00%")&" → "&TEXT(Assump_PropRate,"0.00%")&" · $"&TEXT(Assump_ClosingCosts/1000,"#,##0")&"K closing"` — 9pt italic gray
    - Merge M9:P10; enter `=Refi_DecisionFlag` — 10pt bold, center-aligned; apply same 3-rule conditional formatting as Refinance_Analysis B22
    - _Requirements: 6.1, 6.7, 6.8_

  - [~] 7.7 Insert Chart 1 — Historical Mortgage Rate Trend (A12:H30)
    - Insert a Line chart (no markers) positioned at A12:H30
    - Series 1: X-axis = `tblMortgageRates[observation_date]`, Y-axis = `tblMortgageRates[MORTGAGE30US]` — blue `#2E75B6`, 1.5pt line, legend label "30-Yr Fixed Rate"
    - Series 2: Y-axis = `tblMortgageRates[CurrentRateLine]` — dashed orange `#ED7D31`, legend label "Current Rate"
    - Chart title: `"30-Year Fixed Mortgage Rate — Historical Trend (1971–2026)"`
    - Y-axis: min=0, max=20, label `"Rate (%)"`, horizontal major gridlines only (light gray `#D9D9D9`)
    - Chart area fill: `#F2F7FC`; show legend
    - _Requirements: 6.2_

  - [~] 7.8 Insert Chart 2 — Payment Sensitivity by Rate Scenario (I12:P30)
    - Insert a Clustered Column chart positioned at I12:P30
    - Category axis: `RSM_Labels`; Values: `RSM_Payments`
    - Chart title: `"Monthly P&I Payment by Rate Scenario"`
    - Y-axis label: `"Monthly Payment ($)"`, format `$#,##0`
    - Base Case bar (row 3 of series): color `#375623`; all other bars: `#2E75B6`
    - Data labels: value above each bar, format `$#,##0`
    - Chart area fill: `#F2F7FC`
    - _Requirements: 6.3_

  - [~] 7.9 Insert Chart 3 — Total Lifetime Interest by Scenario (A32:H50)
    - Insert a Clustered Column chart positioned at A32:H50
    - Category axis: `RSM_Labels`; Values: `RSM_TotalInterest`
    - Chart title: `"Total Lifetime Interest by Rate Scenario"`
    - Y-axis label: `"Total Interest ($)"`, format `$#,##0`
    - Base Case bar: `#375623`; all other bars: `#ED7D31`
    - Data labels: value above each bar, format `$#,##0`
    - Chart area fill: `#F2F7FC`
    - _Requirements: 6.4_

  - [~] 7.10 Insert Chart 4 — Affordable Loan Amount by Scenario (I32:P50)
    - Insert a Clustered Column chart positioned at I32:P50
    - Category axis: `RSM_Labels`; Values: `RSM_Affordable`
    - Chart title: `"Affordable Loan Amount at Fixed Monthly Budget"`
    - Y-axis label: `"Affordable Principal ($)"`, format `$#,##0`
    - Base Case bar: `#375623`; all other bars: `#2E75B6`
    - Data labels: value above each bar, format `$#,##0`
    - Chart area fill: `#F2F7FC`
    - _Requirements: 6.5_

  - [x] 7.11 Build Refinance Break-Even Summary table (A52:D62)
    - A51: `"Refinance Break-Even Summary"` — Section Header style
    - Apply KPI card fill `#EBF3FB` to A52:D62
    - Enter 6 labeled rows (column A = label, column B = formula):
      - `Current Payment` | `=Refi_CurrPayment` | format: `$#,##0.00`
      - `New Payment` | `=Refi_NewPayment` | format: `$#,##0.00`
      - `Monthly Savings` | `=Refi_MonthlySavings` | format: `$#,##0.00`
      - `Break-Even` | `=IF(ISNUMBER(Refi_BreakEvenMos),TEXT(Refi_BreakEvenMos,"0.0")&" months","N/A")` | format: `General`
      - `Holding Period` | `=TEXT(Assump_HoldPeriod,"#,##0")&" months"` | format: `General`
      - `Decision` | `=Refi_DecisionFlag` | format: `General` + same 3-rule conditional formatting as Refinance_Analysis B22
    - _Requirements: 6.6_

  - [ ]* 7.12 Verify Dashboard QA checks (default inputs)
    - KPI Card 1 displays `"6.36%"` (default-assumptions test)
    - KPI Card 2 displays ≈ `$2,497` (default-assumptions test)
    - KPI Card 3 displays ≈ `$499,000` (default-assumptions test)
    - KPI Card 4 displays ≈ `$400,400` (default-assumptions test)
    - KPI Card 5 displays `"26.2 months"` (default-assumptions test)
    - Confirm no yellow-filled Input Cells exist on Dashboard (Property 7, Req 6.8)
    - Confirm all 4 charts are visible and populated
    - _Requirements: 6.1–6.9_

- [x] 8. Project_Map final polish
  - Note: Project_Map has no formula dependencies on other sheets. Existing content (title banner, context block, objective section, business question table headers) must be preserved — do not delete anything.

  - [x] 8.1 Polish existing sections to match workbook style
    - Verify title banner A1:F1 uses dark navy fill `#1F3864`, white 16pt bold Calibri, center-aligned
    - Verify context block A3:F4 contains: `Primary Tool: Microsoft Excel`, `Primary Dataset: FRED / Freddie Mac MORTGAGE30US`, `Project Type: Consumer Finance · Financial Modeling · Decision Analytics`
    - Verify Project Objective section A6:F8 is present and complete; apply Section Header style to A6
    - _Requirements: 7.1, 7.2, 7.8_

  - [x] 8.2 Complete Business Questions table rows (A10:E16)
    - A9: `"Business Questions"` — Section Header style
    - Apply column header style to row 10: `ID` | `Business Question` | `Why It Matters` | `Primary Answer Location`
    - Enter 5 data rows (11–15) per the design spec:
      - BQ1: historical rate context → Rate_Data; Dashboard
      - BQ2: payment sensitivity → Rate_Shock_Model; Dashboard
      - BQ3: total interest burden → Rate_Shock_Model; Amortization_Schedule; Dashboard
      - BQ4: affordability at fixed budget → Rate_Shock_Model; Dashboard
      - BQ5: refinance break-even → Refinance_Analysis; Dashboard
    - _Requirements: 7.3_

  - [x] 8.3 Add Workbook Navigation Map table (A18:E26)
    - A17: `"Workbook Navigation Map"` — Section Header style
    - Apply column header style to row 18: `Worksheet` | `Role in the Model` | `Key Outputs` | `BQ Answered`
    - Enter 7 data rows (19–25), one per sheet, per the design spec
    - _Requirements: 7.4_

  - [x] 8.4 Add Formula Family Summary table (A28:C36)
    - A27: `"Core Formula Families"` — Section Header style
    - Apply column header style to row 28: `Formula / Logic` | `Project Use`
    - Enter 8 rows (29–36): PMT, PV, CUMIPMT, PPMT/IPMT, IF, IFERROR, INDEX/MATCH, TEXT/YEAR
    - _Requirements: 7.8_

  - [x] 8.5 Add Scope Notes section (A38:F44)
    - A37: `"Scope — Version 1"` — Section Header style
    - A39:C42: list 5 included items (historical analysis, 6-scenario rate shock, 360-payment amortization, refinance break-even, executive dashboard)
    - D39:F42: list 6 excluded items (taxes/insurance/PMI/HOA, ARM modeling, credit-score pricing, regional differences, VBA macros, AWS pipeline)
    - _Requirements: 7.5_

  - [x] 8.6 Add Portfolio Framing statement (A46:F50)
    - A45: `"Portfolio Framing"` — Section Header style
    - Merge A46:F50; enter the portfolio framing text per the design spec
    - _Requirements: 7.8_

  - [x] 8.7 Add Data Source section (A52:F55) and optional health check formula (F2)
    - A51: `"Data Source"` — Section Header style
    - Enter 6 labeled rows: Source (FRED), Series (MORTGAGE30US), Frequency (Weekly), Date Range, Units, URL
    - F2 (optional): enter workbook health check formula:
      ```
      =IF(ISERROR(Rate_Shock_Model!D13)+ISERROR(Rate_Shock_Model!F13)+ISERROR(Refinance_Analysis!B16)+ISERROR(Amortization_Schedule!I5)>0,"⚠ Errors Detected — Check Model Sheets","✓ All Key Cells OK")
      ```
      Apply conditional formatting: green fill for "✓", red fill for "⚠"
    - _Requirements: 7.6, 7.7_

- [x] 9. Named ranges, formatting, and conditional formatting
  - Depends on: Phases 2–8 complete

  - [x] 9.1 Audit all 29 named ranges in Name Manager
    - Open Formulas → Name Manager and verify all 29 named ranges exist at Workbook scope with correct references:
      - `LatestRate` (1), `Assump_*` (13), `RSM_*` (7), `Refi_*` (5), `Amort_*` (3)
    - Delete any stale or duplicate names
    - Confirm no `#REF!` errors appear in the "Refers To" column
    - _Requirements: 8.5_

  - [x] 9.2 Apply workbook-wide font and color consistency
    - Verify Calibri font is applied to all cells on all 7 sheets
    - Verify title banners use dark navy fill `#1F3864` with white text on all sheets
    - Verify section headers use medium blue fill `#2E75B6` with white text on all sheets
    - Verify column header rows use light blue fill `#D6E4F0` with dark navy text on all sheets
    - Verify Input Cells use yellow fill `#FFF2CC` on Assumptions sheet
    - Verify Formula Cells use white fill (no fill) on all sheets
    - _Requirements: 8.2, 8.3, 8.4_

  - [x] 9.3 Apply number format consistency across all sheets
    - Currency 2dp (`$#,##0.00`): monthly payment cells, amortization columns C–F, refi results B13:B15
    - Currency 0dp (`$#,##0`): loan amount cells, total interest/cost cells, KPI card values
    - Percentage 2dp (`0.00%`): all rate cells across all sheets
    - Percentage 4dp (`0.0000%`): Rate_Data column C (Rate_Decimal) and Amortization B5 (monthly rate)
    - Integer (`#,##0`): term months, holding period, payment number column
    - _Requirements: 8.3_

  - [x] 9.4 Apply cell protection settings
    - On each sheet: select all Input Cells (yellow-filled) → Format Cells → Protection → uncheck Locked
    - All other cells remain locked (default)
    - Apply sheet protection (Review → Protect Sheet) with "Select unlocked cells" allowed; no password required for Version 1
    - _Requirements: 8.4_

  - [x] 9.5 Verify conditional formatting rules are complete
    - Rate_Shock_Model E11:E16: negative = green font `#375623`, positive = red font `#C00000`
    - Refinance_Analysis B22: 3-rule color coding (green/red/red)
    - Dashboard KPI Card 5 M9:P10: same 3-rule color coding
    - Dashboard Refinance Summary Decision cell: same 3-rule color coding
    - Project_Map F2: green fill for "✓", red fill for "⚠"
    - _Requirements: 3.9, 5.6, 6.1_

- [x] 10. QA/testing and workbook health validation
  - Depends on: Phases 2–9 complete

  - [~] 10.1 Run formula-consistency checks (always valid — not input-dependent)
    - Amortization_Schedule I6 = `✓ PASS` (Property 2: total principal = loan amount within $1)
    - Amortization_Schedule I8 = `✓ PASS` (Property 1: ending balance < $1)
    - Rate_Shock_Model E13 = `$0.00` exactly (Property 3: base case delta is zero)
    - Refinance_Analysis B15 = B13 − B14 (Property 6: savings correctness)
    - Project_Map F2 = `"✓ All Key Cells OK"` (Property 7: no errors in key cells)
    - _Requirements: 4.9, 3.4, 5.3, 8.6, 8.7_

  - [~] 10.2 Run default-assumptions benchmark checks
    - Reset all Assumptions inputs to defaults: Home Price $500,000, Down Payment 20%, Budget $2,500, Refi Balance $380,000, Current Rate 7.00%, Proposed Rate 6.00%, Remaining Term 324, Closing Costs $6,000, Holding Period 60
    - Verify: Assumptions B6 = $400,000, B8 = 360, B9 ≈ 6.36%
    - Verify: Rate_Shock_Model D13 ≈ $2,497 (±$5), F13 ≈ $499,000 (±$1,000), H13 ≈ $400,400 (±$500)
    - Verify: Rate_Shock_Model D11 ≈ $2,201 (±$5), D16 ≈ $2,839 (±$5)
    - Verify: Amortization_Schedule I5 ≈ $499,000, B10 = $400,000, E10 ≈ $2,120
    - Verify: Refinance_Analysis B13 ≈ $2,661, B14 ≈ $2,432, B15 ≈ $229, B16 ≈ 26.2, B22 = "Refinance Makes Sense"
    - Verify: Dashboard KPI cards display expected values per Section 11 of design
    - _Requirements: 3.1–3.9, 4.1–4.9, 5.1–5.7, 6.1_

  - [~] 10.3 Run monotonicity checks (formula-consistency — always valid)
    - Confirm D11 < D12 < D13 < D14 < D15 < D16 in Rate_Shock_Model (Property 4: payment increases with rate)
    - Confirm H11 > H12 > H13 > H14 > H15 > H16 in Rate_Shock_Model (Property 5: affordability decreases with rate)
    - _Requirements: 3.3, 3.7_

  - [~] 10.4 Run error-cell scan across all sheets
    - On each sheet, use Ctrl+End to find the used range, then scan for any visible #DIV/0!, #REF!, #VALUE!, or #NAME? errors
    - Alternatively: use Formulas → Error Checking to surface any formula errors
    - All errors must be resolved before proceeding (Property 7)
    - _Requirements: 8.7_

  - [~] 10.5 Run circular reference check
    - Formulas → Error Checking → Circular References — list must be empty
    - _Requirements: 8.6_

  - [~] 10.6 Verify no Input Cells exist on Dashboard
    - Scan Dashboard for any yellow-filled (`#FFF2CC`) cells — none should exist
    - Confirm all Dashboard values are formula references or chart data series
    - _Requirements: 6.8_

  - [~] 10.7 Verify workbook operates without macros or add-ins
    - Close and reopen the workbook with macros disabled (if prompted)
    - Confirm all formulas recalculate correctly and no functionality is lost
    - _Requirements: 8.8_

- [x] 11. Final deliverable review
  - Depends on: Phase 10 complete

  - [~] 11.1 Verify sheet tab order and names
    - Confirm tab order (left to right): Project_Map → Rate_Data → Assumptions → Rate_Shock_Model → Amortization_Schedule → Refinance_Analysis → Dashboard
    - Confirm exactly 7 sheets — no extras
    - _Requirements: 8.1_

  - [~] 11.2 Verify freeze panes and column widths on all sheets
    - Rate_Data: row 1 frozen; Amortization_Schedule: rows 1–9 frozen
    - Confirm column widths match spec for each sheet (Rate_Data, Assumptions, Rate_Shock_Model, Amortization_Schedule, Refinance_Analysis)
    - _Requirements: 8.2_

  - [~] 11.3 Verify all 29 named ranges resolve without errors
    - Open Name Manager; confirm all 29 names show valid cell references with no `#REF!`
    - Spot-check: type `=LatestRate` in a blank cell — should return the latest rate value
    - Delete the test cell after verification
    - _Requirements: 8.5_

  - [~] 11.4 Perform end-to-end input-change test
    - Change Assumptions B4 (Home Price) to $600,000 — confirm B6 updates to $480,000, Rate_Shock_Model D13 updates, Dashboard KPI Card 2 updates
    - Change Assumptions B15 (Current Rate) to 0.065 — confirm Refinance_Analysis B13 updates, Dashboard KPI Card 5 updates
    - Reset all inputs to defaults after testing
    - _Requirements: 2.2, 3.8, 4.8, 5.8, 6.7_

  - [~] 11.5 Save final workbook
    - Save the workbook as `.xlsx` format (no macro-enabled format needed)
    - Confirm file name remains `Rate Shock Loan Affordability & Refinance Dashboard.xlsx`
    - _Requirements: 8.8_


---

## Notes

- Tasks marked with `*` are optional verification/QA sub-tasks and can be skipped for a faster build pass, but are strongly recommended before the final deliverable review
- All formula-consistency checks (I6, I8 PASS/FAIL cells; E13=$0; monotonicity) are always valid regardless of input values — these are the authoritative correctness checks
- Default-assumptions benchmarks (numeric values like $2,497, $499,000, etc.) are only valid when Assumptions holds the exact default values specified in the design
- The workbook must never be rebuilt from scratch — all tasks are additive to the existing file
- Named ranges must be defined at Workbook scope (not sheet scope) so cross-sheet references work correctly
- B7 (Loan Term) is intentionally a Formula Cell (`=30`), not an Input Cell — do not add data validation to it and do not make it editable
- The `CurrentRateLine` column (F) in Rate_Data holds the same value in every row by design — this is correct behavior for the Dashboard chart reference line

---

## Task Dependency Graph

```json
{
  "waves": [
    { "id": 0, "tasks": ["1.1", "1.2"] },
    { "id": 1, "tasks": ["1.3", "2.1"] },
    { "id": 2, "tasks": ["2.2", "2.3", "2.4", "2.5"] },
    { "id": 3, "tasks": ["2.6"] },
    { "id": 4, "tasks": ["2.7", "2.8"] },
    { "id": 5, "tasks": ["2.9", "3.1"] },
    { "id": 6, "tasks": ["3.2", "3.3"] },
    { "id": 7, "tasks": ["3.4", "3.5"] },
    { "id": 8, "tasks": ["3.6", "4.1", "5.1"] },
    { "id": 9, "tasks": ["4.2", "4.3", "5.2"] },
    { "id": 10, "tasks": ["4.4", "4.5", "5.3", "6.1"] },
    { "id": 11, "tasks": ["4.6", "4.7", "4.8", "4.9", "5.4", "6.2"] },
    { "id": 12, "tasks": ["4.10", "5.5", "6.3"] },
    { "id": 13, "tasks": ["4.11", "5.6", "6.4"] },
    { "id": 14, "tasks": ["6.5", "7.1"] },
    { "id": 15, "tasks": ["7.2", "7.3", "7.4", "7.5", "7.6"] },
    { "id": 16, "tasks": ["7.7", "7.8", "7.9", "7.10"] },
    { "id": 17, "tasks": ["7.11", "8.1"] },
    { "id": 18, "tasks": ["7.12", "8.2", "8.3", "8.4", "8.5", "8.6", "8.7"] },
    { "id": 19, "tasks": ["9.1", "9.2", "9.3"] },
    { "id": 20, "tasks": ["9.4", "9.5"] },
    { "id": 21, "tasks": ["10.1", "10.2", "10.3", "10.4", "10.5", "10.6", "10.7"] },
    { "id": 22, "tasks": ["11.1", "11.2", "11.3"] },
    { "id": 23, "tasks": ["11.4"] },
    { "id": 24, "tasks": ["11.5"] }
  ]
}
```
