# Phase 3: SQL / Cloud Data Layer

**Status**: 🔲 Planned  
**Timeline**: TBD  
**Tech Stack**: SQL (specific platform TBD)

---

## Overview

Phase 3 will build a structured data layer for querying and joining the mortgage rate series with additional economic context. This phase is a **future extension** — specific platform decisions (cloud provider, database engine, orchestration tools) have not been made.

This phase is **not yet implemented**. This folder is a placeholder for future SQL scripts and documentation.

---

## Planned Objectives

- Centralized storage for FRED rate data and derived analytics
- SQL queries for historical rate analysis, aggregations, and trend summaries
- Potential integration with additional FRED series (e.g., unemployment rate, federal funds rate, inflation)
- Documentation: data pipeline architecture and query library

---

## Planned Business Questions

9. How do mortgage rates correlate with other macroeconomic indicators?
10. Can we identify leading indicators for mortgage rate movements?

---

## Planned Tech Stack (Tentative)

Platform decisions are not finalized. Options under consideration:

- **SQL database**: PostgreSQL, SQLite, or cloud-native (AWS Athena, BigQuery)
- **Storage**: Local CSV, S3, or equivalent cloud storage
- **Ingestion**: Python scripts for FRED API data pulls
- **Orchestration**: TBD based on complexity needs

> **Note:** AWS, Databricks, Terraform, and other cloud-native tooling are aspirational ideas only — not committed or scoped.

---

## Planned Folder Structure (Tentative)

```
phase3_sql/
├── README.md                      # This file
├── sql/
│   ├── create_tables.sql          # Table definitions
│   ├── historical_analysis.sql    # Historical rate analysis queries
│   └── aggregations.sql           # Monthly/yearly aggregations
├── scripts/
│   ├── ingest_fred.py             # FRED data ingestion
│   └── requirements.txt           # Python dependencies
└── docs/
    ├── architecture.md            # Data pipeline architecture
    └── query_library.md           # SQL query library
```

---

## Example Queries (Planned)

### Monthly Average Mortgage Rate

```sql
SELECT
    DATE_TRUNC('month', date) AS month,
    AVG(mortgage30us) AS avg_rate
FROM mortgage_rates
GROUP BY DATE_TRUNC('month', date)
ORDER BY month;
```

### Rate Change Detection

```sql
SELECT
    date,
    mortgage30us,
    LAG(mortgage30us) OVER (ORDER BY date) AS prev_rate,
    mortgage30us - LAG(mortgage30us) OVER (ORDER BY date) AS weekly_change
FROM mortgage_rates
WHERE ABS(mortgage30us - LAG(mortgage30us) OVER (ORDER BY date)) > 0.25
ORDER BY date;
```

---

## Status Disclaimer

**This phase is planned but not yet implemented.** This folder is a placeholder to signal the multi-phase vision of the QuantPath Mortgage Rate Intelligence Stack.

All content in this README is aspirational and subject to change based on coursework requirements, technical constraints, and portfolio priorities. Cloud platform decisions, cost analysis, and architecture diagrams will be developed when this phase is actively scoped.

---

## References

- **FRED API**: https://fred.stlouisfed.org/docs/api/fred/
- **PostgreSQL**: https://www.postgresql.org/docs/
- **AWS Athena**: https://aws.amazon.com/athena/ (exploratory only)
