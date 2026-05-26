# Documentation Style Guide

## Tone and Voice

- **Professional** — suitable for LinkedIn, recruiters, instructors, and technical reviewers
- **Honest** — never overclaim; clearly separate completed vs. planned vs. future
- **Accessible** — understandable to both technical and non-technical audiences
- **Confident but measured** — demonstrate competence without overpromising

---

## Target Audience

The repository documentation should be understandable to:

- Technical reviewers (data analysts, engineers)
- Non-technical stakeholders (hiring managers, advisors)
- Recruiters
- Instructors and academic evaluators
- Project-management reviewers
- Future me (for maintainability)

---

## Key Framing Rules

### IBM Certificate Context

Frame as applied learning, not a capstone:

> "This project connects applied Excel-based analytics, financial modeling, and R-focused learning from IBM's Data Analytics with Excel and R Professional Certificate with my broader QuantPath analytics portfolio."

Always include the disclaimer:
> "The IBM certificate connection represents applied learning — not a completed IBM capstone project."

### QuantPath Branding

- Use `QuantPath™` sparingly (once per document or post maximum)
- Never use `®`
- Do not position QuantPath as a company or product — it is a personal learning system

### Phase Status Honesty

Always clearly label:
- ✅ Complete — only for Phase 1
- 🔲 Planned — for Phase 2 R analysis
- 🔲 Future — for Phase 3+, Databricks, Tableau, AWS, Streamlit, agents

Never describe planned work as if it already exists.

### Phase 2 (R) Framing

Do NOT overcenter on:
- Advanced forecasting
- ARIMA/GARCH production models
- Machine learning pipelines
- Prediction accuracy claims

DO describe as:
- Time-series analysis fundamentals
- Stationarity testing, rolling statistics, regime analysis
- Seasonality checks, historical percentile context
- Optional baseline forecasting (exploratory)
- Connected to NCCU coursework and IBM certificate learning path

---

## Preferred Repository Description

Short:
> Excel financial modeling project translating FRED/Freddie Mac mortgage-rate data into affordability, payment-sensitivity, lifetime-interest, and refinance decision-support outputs.

Full:
> A phased financial analytics project that begins with an Excel decision-support model and expands into R-based time-series analysis, SQL analytics, and future interactive presentation layers.

---

## Core Message

> Mortgage-rate changes are usually discussed as macroeconomic headlines, but households experience them through monthly payments, lifetime borrowing cost, purchasing power, and refinance tradeoffs. This project translates a public mortgage-rate series into a reusable decision-support system that makes those financial impacts easier to understand.

---

## Documentation Emphasis

The repo should emphasize:
- Business questions (what problems does this solve?)
- Financial modeling logic (how does it work?)
- Reproducibility (can someone else use this?)
- Applied learning (what skills does this demonstrate?)
- Documentation quality (is this well-organized?)
- Phased delivery (what's done, what's next?)
- Decision-support value (why does this matter?)

---

## LinkedIn / Public Communication

Phase 1 posts should emphasize:
- Phase 1 complete
- Excel financial modeling
- Public mortgage-rate data (FRED/Freddie Mac)
- Reusable financial modeling template
- Decision-support tool, not just a calculator
- Business questions answered
- Key Excel functions used
- IBM certificate learning path connection
- Phase 2 R/time-series roadmap
- QuantPath™ analytics stack (use once)

---

## Formatting Conventions

- Use tables for structured comparisons (phase status, business questions, etc.)
- Use blockquotes for preferred wording templates
- Use bold for key terms on first appearance
- Use code formatting for function names, file paths, and series IDs
- Include explicit scope notes where workbook limitations apply
- Include status disclaimers for planned/future work
- Keep README sections scannable with clear headers
