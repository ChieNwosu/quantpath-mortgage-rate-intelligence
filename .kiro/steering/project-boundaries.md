# Project Boundaries — Kiro Usage Rules

## Critical Constraints

1. **Do NOT modify the finalized Excel workbook** unless explicitly asked.
2. **Do NOT rebuild the workbook** or change formulas.
3. **Do NOT run scripts, PowerShell, or automated workbook edits** unless explicitly approved.
4. **Do NOT overstate planned phases as completed.**
5. **Do NOT claim the workbook models** income, DTI, credit score, taxes, insurance, PMI, ARM loans, HOA fees, or regional housing prices.
6. **Do NOT implement Bedrock or paid cloud dependencies** unless explicitly requested.

---

## Phase Status Rules

| Phase | Rule |
|-------|------|
| Phase 1 (Excel) | Treat as **complete and finalized**. Do not modify. |
| Phase 2 (R) | Treat as **planned / in progress only**. Do not claim it is done. |
| Phase 3 (SQL) | Treat as **future extension**. Platform TBD. |
| Databricks, Tableau, AWS | **Future extensions only** — not scoped or scheduled. |
| Streamlit app | **Future extension** — not started. |
| GitHub Pages | **Optional** — not required for Phase 1. |
| Agent / chatbot layer | **Future extension** — prefer open-source/cost-conscious baseline. |

---

## What Kiro Should Do

- Documentation engineer
- Repo organizer
- Specification writer
- README builder
- Project-management artifact generator
- GitHub Pages / static site builder (when requested)
- Code scaffold generator (after approval)

---

## What Kiro Should NOT Do

- Overwrite or "fix" finalized artifacts without approval
- Add GitHub Pages unless requested
- Add a backend, chatbot, Streamlit app, or dynamic demo unless requested
- Claim features that don't exist in the workbook
- Run scripts or automated tooling against the workbook
- Force-push, delete branches, or make destructive git operations without approval
- Create files that overstate project completeness

---

## Workbook Scope (What It Actually Models)

Configurable inputs on the Assumptions sheet:
- Home price
- Down payment percentage
- Loan amount (derived)
- Latest / base mortgage rate (from FRED data)
- Target monthly payment budget
- Remaining refinance balance
- Current mortgage rate
- Proposed refinance rate
- Closing costs
- Expected holding period

What it does NOT model:
- Income or annual salary
- Monthly debt obligations
- Debt-to-income (DTI) ratio
- Credit score
- Property taxes
- Homeowners insurance
- Private mortgage insurance (PMI)
- Adjustable-rate mortgages (ARMs)
- HOA fees
- Regional housing prices
- Co-borrower scenarios
- Prepayment / extra principal

---

## Git Workflow Preferences

- Always push to a new branch, never directly to main/master
- Create pull requests for review before merging
- Use descriptive commit messages
- Documentation-only changes are preferred for repo polish tasks
- Do not delete files without explicit approval
