# Data

This folder contains the raw mortgage rate dataset used in Phase 1 of the QuantPath Mortgage Rate Intelligence Stack.

---

## Dataset: MORTGAGE30US

**Series**: 30-Year Fixed Rate Mortgage Average in the United States  
**Provider**: Federal Reserve Bank of St. Louis (FRED) / Freddie Mac Primary Mortgage Market Survey  
**Frequency**: Weekly (Thursday)  
**Coverage**: April 2, 1971 – May 14, 2026 (~2,877 observations)  
**Units**: Percent, not seasonally adjusted  
**URL**: https://fred.stlouisfed.org/series/MORTGAGE30US

---

## File: `raw/MORTGAGE30US.csv`

### Columns

| Column | Type | Description |
|---|---|---|
| `DATE` | Date (YYYY-MM-DD) | Observation date (weekly Thursday) |
| `MORTGAGE30US` | Numeric (percent) | 30-year fixed mortgage rate |

### Sample Rows

```
DATE,MORTGAGE30US
1971-04-02,7.33
1971-04-09,7.29
...
2026-05-14,6.36
```

### Data Quality Notes

- No missing values in the FRED series
- Rates are reported as percentages (e.g., `6.36` = 6.36%)
- The workbook converts these to decimal form for Excel financial functions (e.g., `6.36%` → `0.0636`)

---

## Data Provenance

The dataset was downloaded directly from FRED on May 19, 2026. The FRED series is updated weekly and reflects Freddie Mac's Primary Mortgage Market Survey, which tracks the average interest rate on 30-year fixed-rate mortgages with conforming loan balances.

---

## Usage in Workbook

The `Rate_Data` sheet in the Excel workbook imports this CSV and adds helper columns:
- `Rate_Decimal`: converts percent to decimal for Excel formulas
- `Year`: extracts year from DATE
- `Month_Year`: formats as "MMM YYYY" for chart labels
- `CurrentRateLine`: marks the latest rate for chart annotation

The `Assumptions` sheet references the latest rate via the named range `Assump_BaseRate`.

---

## Future Phases

- **Phase 2 (R)**: Time-series analysis — stationarity testing, rolling statistics, regime analysis, seasonality checks (planned)
- **Phase 3 (SQL)**: Structured data layer for querying and joining with additional economic indicators (planned)
