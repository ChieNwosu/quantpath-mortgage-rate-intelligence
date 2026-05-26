# Requirements Document

## Introduction

The Rate Shock Loan Affordability & Refinance Dashboard is an Excel workbook that converts historical U.S. 30-year fixed mortgage rate data into practical household finance scenarios and decision-support outputs. The workbook answers five business questions covering historical rate context, payment sensitivity, lifetime interest burden, affordability at a fixed budget, and refinance break-even analysis. The deliverable is a polished, portfolio-ready financial analytics product built entirely within Excel using native formula families (PMT, PV, CUMIPMT, PPMT, IF, SUMIFS, INDEX/MATCH).

The workbook already contains a populated Rate_Data sheet and partially started Project_Map. All remaining sheets (Assumptions, Rate_Shock_Model, Amortization_Schedule, Refinance_Analysis, Dashboard) must be built out according to this specification. No new sheets shall be created and no existing sheet names shall be changed.

---

## Glossary

- **Workbook**: The Excel file "Rate Shock Loan Affordability & Refinance Dashboard.xlsx"
- **Rate_Data**: The worksheet containing imported FRED/Freddie Mac weekly 30-year fixed mortgage rate data
- **Assumptions**: The worksheet serving as the central user-input and scenario-control panel
- **Rate_Shock_Model**: The worksheet performing interest-rate scenario analysis across six rate scenarios
- **Amortization_Schedule**: The worksheet computing the full 360-payment base-case loan amortization
- **Refinance_Analysis**: The worksheet computing refinance comparison and break-even metrics
- **Dashboard**: The worksheet presenting KPI cards and charts as an executive visual summary
- **Project_Map**: The worksheet serving as the one-page executive overview and workbook navigation guide
- **Base Case**: The scenario using the latest 30-year fixed mortgage rate pulled directly from Rate_Data
- **Rate Shock**: A scenario in which the mortgage rate is shifted by a fixed increment (−1.00%, −0.50%, +0.50%, +1.00%, +1.50%) relative to the Base Case
- **Monthly P&I Payment**: The monthly principal-and-interest payment computed by the PMT function; excludes taxes, insurance, PMI, and HOA fees
- **Affordable Principal**: The maximum loan amount a borrower can carry given a fixed monthly P&I budget, computed by the PV function
- **Break-Even Months**: The number of months required for cumulative monthly savings from refinancing to recover closing costs
- **Decision Flag**: A text label ("Refinance Makes Sense" or "Does Not Break Even in Holding Period") produced by an IF formula comparing Break-Even Months to the expected holding period
- **KPI Card**: A prominently formatted cell or cell range on the Dashboard displaying a single key metric with a label
- **MORTGAGE30US**: The column in Rate_Data containing the weekly 30-year fixed mortgage rate expressed as a percentage (e.g., 6.36)
- **Rate_Decimal**: A helper column in Rate_Data equal to MORTGAGE30US divided by 100
- **Input Cell**: A cell intended for direct user entry; visually distinguished by a fill color or border style
- **Formula Cell**: A cell whose value is derived by an Excel formula; must not be manually overwritten

---

## Requirements

### Requirement 1: Rate_Data — Historical Rate Preparation

**User Story:** As a financial analyst, I want the Rate_Data sheet to expose summary statistics and helper columns derived from the raw FRED data, so that all other sheets can reference a single authoritative source for rate values and historical context.

#### Acceptance Criteria

1. THE Rate_Data Sheet SHALL retain the original `observation_date` and `MORTGAGE30US` columns without modification to any existing values.
2. THE Rate_Data Sheet SHALL contain a `Rate_Decimal` helper column whose value for each row equals the corresponding `MORTGAGE30US` value divided by 100.
3. THE Rate_Data Sheet SHALL contain a `Year` helper column whose value for each row equals the four-digit year extracted from `observation_date` using the YEAR function.
4. THE Rate_Data Sheet SHALL contain a `Month_Year` helper column whose value for each row is a text label formatted as "MMM-YYYY" derived from `observation_date` using the TEXT function.
5. THE Rate_Data Sheet SHALL contain a named summary block that exposes the following five statistics, each computed by formula from the `MORTGAGE30US` column: Latest Rate, Historical Minimum, Historical Maximum, Historical Average, and 52-Week Trailing Average.
6. WHEN the source data in the `MORTGAGE30US` column is updated with new weekly observations, THE Rate_Data Sheet SHALL automatically recalculate all helper columns and summary statistics without requiring manual intervention.
7. THE Rate_Data Sheet SHALL clearly distinguish the summary block from the raw data table using a visible section header label.

---

### Requirement 2: Assumptions — Central Input Panel

**User Story:** As a borrower or analyst, I want a single sheet where I can enter all loan and refinance inputs, so that every model sheet derives its values from one controlled location and scenario changes require editing only one place.

#### Acceptance Criteria

1. THE Assumptions Sheet SHALL contain the following loan input fields, each in a dedicated Input Cell: Home Price, Down Payment Percentage, Loan Term (years), and Target Monthly P&I Budget.
2. THE Assumptions Sheet SHALL contain a Loan Amount Formula Cell that computes Home Price multiplied by (1 minus Down Payment Percentage).
3. THE Assumptions Sheet SHALL contain a Base Mortgage Rate Formula Cell that references the Latest Rate value from the Rate_Data summary block, so that the base rate updates automatically when Rate_Data is refreshed.
4. THE Assumptions Sheet SHALL contain the following refinance input fields, each in a dedicated Input Cell: Remaining Balance, Current Mortgage Rate, Proposed Refinance Rate, Remaining Term (months), Closing Costs, and Expected Holding Period (months).
5. THE Assumptions Sheet SHALL visually separate loan inputs from refinance inputs using distinct section headers.
6. THE Assumptions Sheet SHALL visually distinguish Input Cells from Formula Cells using a consistent formatting convention (e.g., a distinct fill color for input cells).
7. IF a user enters a Down Payment Percentage greater than 100% or less than 0%, THEN THE Assumptions Sheet SHALL display a validation warning using Excel Data Validation to prevent invalid input.
8. IF a user enters a Loan Term value that is not a positive integer, THEN THE Assumptions Sheet SHALL display a validation warning using Excel Data Validation to prevent invalid input.

---

### Requirement 3: Rate_Shock_Model — Scenario Analysis

**User Story:** As a financial analyst, I want to see how monthly payments, total interest, total loan cost, and affordable principal change across six rate scenarios, so that I can quantify the financial impact of rate movements on a representative borrower.

#### Acceptance Criteria

1. THE Rate_Shock_Model Sheet SHALL contain exactly six scenario rows with the following rate offsets applied to the Base Case rate: −1.00%, −0.50%, 0.00% (Base Case), +0.50%, +1.00%, and +1.50%.
2. THE Rate_Shock_Model Sheet SHALL contain a Scenario Rate Formula Cell for each row that adds the scenario offset to the Base Mortgage Rate from the Assumptions sheet.
3. THE Rate_Shock_Model Sheet SHALL contain a Monthly Payment Formula Cell for each scenario row computed using the PMT function applied to the Scenario Rate, Loan Term, and Loan Amount from the Assumptions sheet.
4. THE Rate_Shock_Model Sheet SHALL contain a Payment Change vs. Base Formula Cell for each scenario row that expresses the difference between the scenario Monthly Payment and the Base Case Monthly Payment as a dollar amount.
5. THE Rate_Shock_Model Sheet SHALL contain a Total Lifetime Interest Formula Cell for each scenario row computed using the CUMIPMT function over the full loan term.
6. THE Rate_Shock_Model Sheet SHALL contain a Total Loan Cost Formula Cell for each scenario row equal to the sum of the Loan Amount and Total Lifetime Interest.
7. THE Rate_Shock_Model Sheet SHALL contain an Affordable Principal Formula Cell for each scenario row computed using the PV function applied to the Scenario Rate, Loan Term, and Target Monthly P&I Budget from the Assumptions sheet.
8. WHEN the Base Mortgage Rate in Assumptions changes, THE Rate_Shock_Model Sheet SHALL automatically recalculate all six scenario rows without requiring manual intervention.
9. THE Rate_Shock_Model Sheet SHALL label the Base Case row distinctly (e.g., bold font or highlighted fill) so it is immediately identifiable.

---

### Requirement 4: Amortization_Schedule — Base-Case Loan Mechanics

**User Story:** As a borrower, I want to see the full month-by-month breakdown of principal and interest payments for the base-case loan, so that I can understand how my balance declines and how much interest I pay in each period.

#### Acceptance Criteria

1. THE Amortization_Schedule Sheet SHALL contain exactly 360 data rows, one for each monthly payment of a 30-year loan.
2. THE Amortization_Schedule Sheet SHALL contain a Payment Number column with sequential integers from 1 to 360.
3. THE Amortization_Schedule Sheet SHALL contain a Beginning Balance Formula Cell for each row: row 1 equals the Loan Amount from Assumptions; each subsequent row equals the Ending Balance of the prior row.
4. THE Amortization_Schedule Sheet SHALL contain a Payment Formula Cell for each row computed using the PMT function at the Base Case rate, Loan Term, and Loan Amount from Assumptions.
5. THE Amortization_Schedule Sheet SHALL contain a Principal Paid Formula Cell for each row computed using the PPMT function.
6. THE Amortization_Schedule Sheet SHALL contain an Interest Paid Formula Cell for each row computed using the IPMT function.
7. THE Amortization_Schedule Sheet SHALL contain an Ending Balance Formula Cell for each row equal to Beginning Balance minus Principal Paid.
8. WHEN the Base Mortgage Rate or Loan Amount in Assumptions changes, THE Amortization_Schedule Sheet SHALL automatically recalculate all 360 rows without requiring manual intervention.
9. THE Amortization_Schedule Sheet SHALL display a summary row or block above or below the 360-row table showing: Total Payments, Total Principal Paid, and Total Interest Paid, each computed by summing the respective column.

---

### Requirement 5: Refinance_Analysis — Break-Even Model

**User Story:** As a homeowner considering refinancing, I want to see my current vs. new payment, monthly savings, break-even timeline, and a clear decision flag, so that I can determine whether refinancing is financially worthwhile given my expected holding period.

#### Acceptance Criteria

1. THE Refinance_Analysis Sheet SHALL contain a Current Payment Formula Cell computed using the PMT function applied to the Current Mortgage Rate, Remaining Term, and Remaining Balance from Assumptions.
2. THE Refinance_Analysis Sheet SHALL contain a New Payment Formula Cell computed using the PMT function applied to the Proposed Refinance Rate, Remaining Term, and Remaining Balance from Assumptions.
3. THE Refinance_Analysis Sheet SHALL contain a Monthly Savings Formula Cell equal to the absolute difference between Current Payment and New Payment.
4. THE Refinance_Analysis Sheet SHALL contain a Break-Even Months Formula Cell equal to Closing Costs divided by Monthly Savings.
5. IF Monthly Savings is zero or negative, THEN THE Refinance_Analysis Sheet SHALL display "No Savings — Refinance Not Beneficial" in the Decision Flag cell instead of performing a division that would produce an error or misleading result.
6. THE Refinance_Analysis Sheet SHALL contain a Decision Flag Formula Cell that displays "Refinance Makes Sense" WHEN Break-Even Months is less than or equal to the Expected Holding Period from Assumptions, and displays "Does Not Break Even in Holding Period" otherwise.
7. THE Refinance_Analysis Sheet SHALL display all key inputs (Current Rate, Proposed Rate, Remaining Balance, Closing Costs, Holding Period) as labeled read-only references to Assumptions, so the analyst can verify inputs without switching sheets.
8. WHEN any refinance input in Assumptions changes, THE Refinance_Analysis Sheet SHALL automatically recalculate all outputs without requiring manual intervention.

---

### Requirement 6: Dashboard — Executive Visual Summary

**User Story:** As a portfolio reviewer or stakeholder, I want a single-sheet visual summary of the most important metrics and charts, so that I can assess the rate environment, payment sensitivity, and refinance decision at a glance without navigating other sheets.

#### Acceptance Criteria

1. THE Dashboard Sheet SHALL contain five KPI Cards displaying the following metrics: Latest Mortgage Rate (from Rate_Data), Base Case Monthly Payment (from Rate_Shock_Model), Total Lifetime Interest at Base Case (from Rate_Shock_Model), Affordable Loan Amount at Target Payment and Base Rate (from Rate_Shock_Model), and Refinance Break-Even Months (from Refinance_Analysis).
2. THE Dashboard Sheet SHALL contain a Historical Mortgage Rate Trend chart plotting `observation_date` on the x-axis and `MORTGAGE30US` on the y-axis using data from Rate_Data, covering the full available history.
3. THE Dashboard Sheet SHALL contain a Payment Sensitivity chart plotting Monthly Payment on the y-axis against the six scenario rate labels on the x-axis using data from Rate_Shock_Model.
4. THE Dashboard Sheet SHALL contain a Total Interest by Scenario chart plotting Total Lifetime Interest on the y-axis against the six scenario rate labels on the x-axis using data from Rate_Shock_Model.
5. THE Dashboard Sheet SHALL contain an Affordable Loan Amount by Scenario chart plotting Affordable Principal on the y-axis against the six scenario rate labels on the x-axis using data from Rate_Shock_Model.
6. THE Dashboard Sheet SHALL contain a Refinance Break-Even Summary visual element (chart or formatted table) that displays Monthly Savings, Break-Even Months, and the Decision Flag from Refinance_Analysis.
7. WHEN any upstream input in Assumptions changes, THE Dashboard Sheet KPI Cards SHALL automatically reflect the updated values without requiring manual intervention.
8. THE Dashboard Sheet SHALL not contain any Input Cells; all values SHALL be formula references or chart data series linked to other sheets.
9. THE Dashboard Sheet SHALL use consistent formatting (font family, color palette, number formats) across all KPI Cards and chart titles.

---

### Requirement 7: Project_Map — Executive Overview and Navigation Guide

**User Story:** As a portfolio reviewer, I want a clean one-page landing sheet that explains the project objective, lists the five business questions, maps each sheet's role, and defines the scope, so that I can understand the entire workbook without opening any other sheet.

#### Acceptance Criteria

1. THE Project_Map Sheet SHALL display the project title "Rate Shock Loan Affordability & Refinance Dashboard" as a prominent header.
2. THE Project_Map Sheet SHALL contain a Project Objective section that states the analytical purpose of the workbook in plain language.
3. THE Project_Map Sheet SHALL contain a Business Questions section listing all five business questions (BQ1 through BQ5) with their associated source sheets.
4. THE Project_Map Sheet SHALL contain a Workbook Navigation Map table with one row per sheet, showing Sheet Name, Role, and Key Outputs for all seven sheets.
5. THE Project_Map Sheet SHALL contain a Scope section that explicitly lists what is included in Version 1 and what is excluded.
6. THE Project_Map Sheet SHALL contain a Data Source section crediting FRED/Freddie Mac as the source of the mortgage rate data and stating the date range covered (April 2, 1971 through the latest available observation).
7. THE Project_Map Sheet SHALL not contain any Input Cells or formula-driven model outputs; it SHALL serve as a static documentation page.
8. THE Project_Map Sheet SHALL be readable and self-contained without requiring the reviewer to open any other sheet.

---

### Requirement 8: Workbook-Wide Consistency and Integrity

**User Story:** As a developer building this workbook and as a reviewer evaluating it, I want consistent formatting, naming, and formula conventions across all sheets, so that the workbook reads as a single coherent product rather than a collection of disconnected worksheets.

#### Acceptance Criteria

1. THE Workbook SHALL use exactly seven sheets with the names: Project_Map, Rate_Data, Assumptions, Rate_Shock_Model, Amortization_Schedule, Refinance_Analysis, Dashboard — no additional sheets SHALL be created and no existing sheet names SHALL be changed.
2. THE Workbook SHALL use a consistent font family across all sheets.
3. THE Workbook SHALL apply a consistent number format for currency values (e.g., `$#,##0.00`), percentage values (e.g., `0.00%`), and integer counts across all sheets.
4. THE Workbook SHALL use a consistent color convention to distinguish Input Cells (user-editable) from Formula Cells (formula-driven) across all sheets.
5. WHEN a Formula Cell on any sheet references an input from Assumptions, THE Formula Cell SHALL use a direct cell reference or named range pointing to Assumptions rather than hard-coding a value.
6. THE Workbook SHALL not contain any circular references.
7. THE Workbook SHALL not display any #DIV/0!, #REF!, #VALUE!, or #NAME? errors in any visible cell under the default Assumptions inputs.
8. THE Workbook SHALL be operable in Microsoft Excel (desktop) without requiring macros, VBA, or add-ins.
