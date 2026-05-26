# Phase 3: SQL / Cloud Data Layer

**Status**: 🔲 Planned  
**Timeline**: Fall 2026  
**Tech Stack**: AWS S3, AWS Athena, SQL, Python (boto3)

---

## Overview

Phase 3 will build a cloud-native data layer for scalable storage, querying, and integration with additional economic indicators.

This phase is **not yet implemented**. This folder is a placeholder for future SQL scripts, Python ingestion scripts, and documentation.

---

## Planned Objectives

- Build AWS S3 bucket for raw FRED data storage
- Set up AWS Glue Data Catalog for schema management
- Configure AWS Athena for serverless SQL querying
- Develop Python scripts for automated FRED data ingestion to S3
- Write SQL queries for historical rate analysis, aggregations, and joins
- Integrate additional FRED series (e.g., unemployment rate, GDP, inflation)
- Document data pipeline architecture, SQL query library, and AWS setup guide

---

## Planned Business Questions

9. How do mortgage rates correlate with other macroeconomic indicators (unemployment, GDP, inflation)?
10. Can we identify leading indicators for mortgage rate movements?

---

## Planned Tech Stack

- **Storage**: AWS S3 (raw CSV files)
- **Catalog**: AWS Glue Data Catalog (schema registry)
- **Query**: AWS Athena (serverless SQL)
- **Orchestration**: AWS Lambda or Step Functions for automated ingestion
- **IAM**: Least-privilege access policies for S3 and Athena
- **Python**: `boto3` for AWS SDK, `pandas` for data manipulation

---

## Planned Folder Structure

```
phase3_sql/
├── README.md                      # This file
├── sql/
│   ├── create_tables.sql          # Athena table definitions
│   ├── historical_analysis.sql    # Historical rate analysis queries
│   ├── correlation_analysis.sql   # Correlation with other indicators
│   └── aggregations.sql           # Monthly/yearly aggregations
├── python/
│   ├── ingest_fred_to_s3.py       # FRED data ingestion to S3
│   ├── setup_glue_catalog.py      # Glue Data Catalog setup
│   └── requirements.txt           # Python dependencies
├── terraform/                     # Infrastructure-as-code (optional)
│   ├── main.tf                    # Terraform main configuration
│   ├── s3.tf                      # S3 bucket configuration
│   ├── glue.tf                    # Glue Data Catalog configuration
│   └── athena.tf                  # Athena configuration
└── docs/
    ├── architecture.md            # Data pipeline architecture
    ├── setup_guide.md             # AWS setup guide
    └── query_library.md           # SQL query library
```

---

## Planned Deliverables

- [ ] AWS S3 bucket for raw FRED data storage
- [ ] AWS Glue Data Catalog for schema management
- [ ] AWS Athena for SQL querying
- [ ] Python scripts for automated FRED data ingestion to S3
- [ ] SQL queries for historical rate analysis, aggregations, and joins
- [ ] Integration with additional FRED series (e.g., unemployment rate, GDP, inflation)
- [ ] Documentation: data pipeline architecture, SQL query library, AWS setup guide

---

## Planned AWS Architecture

```
┌─────────────────┐
│  FRED API       │
└────────┬────────┘
         │
         │ Python (boto3)
         │
         ▼
┌─────────────────┐
│  AWS S3         │  ← Raw CSV files
│  (Data Lake)    │
└────────┬────────┘
         │
         │ AWS Glue Crawler
         │
         ▼
┌─────────────────┐
│  AWS Glue       │  ← Schema registry
│  Data Catalog   │
└────────┬────────┘
         │
         │ SQL queries
         │
         ▼
┌─────────────────┐
│  AWS Athena     │  ← Serverless SQL
└─────────────────┘
```

---

## Planned Data Sources

### Primary Series

- **MORTGAGE30US**: 30-Year Fixed Rate Mortgage Average

### Additional Series (Planned)

- **UNRATE**: Unemployment Rate
- **GDP**: Gross Domestic Product
- **CPIAUCSL**: Consumer Price Index (Inflation)
- **FEDFUNDS**: Federal Funds Effective Rate
- **DGS10**: 10-Year Treasury Constant Maturity Rate

---

## Planned SQL Queries

### Historical Rate Analysis

```sql
-- Monthly average mortgage rate
SELECT 
    DATE_TRUNC('month', date) AS month,
    AVG(mortgage30us) AS avg_rate
FROM mortgage_rates
GROUP BY DATE_TRUNC('month', date)
ORDER BY month;
```

### Correlation Analysis

```sql
-- Correlation between mortgage rates and unemployment
SELECT 
    CORR(m.mortgage30us, u.unrate) AS correlation
FROM mortgage_rates m
JOIN unemployment u ON m.date = u.date;
```

### Rate Shock Scenarios

```sql
-- Count of weeks where rate changed by more than 0.50%
SELECT 
    COUNT(*) AS shock_weeks
FROM (
    SELECT 
        date,
        mortgage30us,
        LAG(mortgage30us) OVER (ORDER BY date) AS prev_rate,
        ABS(mortgage30us - LAG(mortgage30us) OVER (ORDER BY date)) AS rate_change
    FROM mortgage_rates
) subquery
WHERE rate_change > 0.50;
```

---

## Cost Considerations

- **S3 storage**: ~$0.023/GB/month (Standard tier)
- **Athena queries**: $5 per TB scanned
- **Glue Crawler**: $0.44 per DPU-hour
- **Lambda**: Free tier covers most ingestion workloads

**Estimated monthly cost**: <$5 for this project's data volume.

---

## Status Disclaimer

**This phase is planned but not yet implemented.** This folder is a placeholder to signal the multi-phase vision of the QuantPath Mortgage Rate Intelligence Stack. All content in this folder is aspirational and subject to change based on coursework requirements, technical constraints, and portfolio priorities.

---

## References

- **AWS S3**: https://aws.amazon.com/s3/
- **AWS Athena**: https://aws.amazon.com/athena/
- **AWS Glue**: https://aws.amazon.com/glue/
- **FRED API**: https://fred.stlouisfed.org/docs/api/fred/
- **boto3 documentation**: https://boto3.amazonaws.com/v1/documentation/api/latest/index.html
