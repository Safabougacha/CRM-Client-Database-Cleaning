# 🌿 Project CAM — CRM Client Database Cleaning & ERP Matching

> **M2 Data Science Internship Project** · Python · pandas · openpyxl  
> Automated classification and ERP reconciliation of 21,000+ Salesforce CRM accounts

---

## 📌 Context

Large CRM databases accumulate thousands of stale, closed, or inactive accounts over time. This project built a fully automated Python pipeline to clean a Salesforce CRM base for a agri-food company's commercial division — cross-referencing 6 data sources to classify every account and match it to the ERP system.

**Scale:** 21,210 accounts processed · 6 sources cross-referenced · run time: seconds

---

## 🎯 Objectives

- **Classify** every CRM account into one of three statuses with a detailed, auditable reason
- **Cross-reference** 6 heterogeneous data sources (Salesforce exports, billing history, ERP)
- **Match** in-scope accounts to their ERP client codes for financial reconciliation
- **Deliver** a production-ready, color-coded, multi-sheet Excel report

---

## 📊 Final Results

| Status | Count | % |
|---|---|---|
| 🟢 **Keep** | 6,480 | 30.5% |
| 🔴 **Deactivate** | 14,633 | 69.0% |
| 🟠 **Reintegrate** | 97 | 0.5% |
| **Total** | **21,210** | 100% |

**ERP matching coverage: 98.8%** (20,958 / 21,210 accounts matched)

---

## 🗂️ Data Sources

| File | Volume | Role |
|---|---|---|
| `accounts.csv` | 24,066 rows | Main CRM source — status, dates, billing country |
| `opportunities.csv` | 10,472 rows | Sales opportunities per account |
| `invoice_history.csv` | 112,843 rows | Full billing history |
| `active_invoices.csv` | 4,272 rows | Currently unpaid invoices |
| `last_contact.xlsx` | 29,430 rows | Last CRM contact per account |
| `events.xlsx` | 42,659 rows | Last CRM event per account |
| `erp_clients.xlsx` | ~20,000 rows | ERP reference base for matching |

> ⚠️ Source files are not included in this repository (proprietary data). See `data_samples/` for anonymized schema examples.

---

## 🔄 Pipeline — 5 Sequential Steps

```
Step 1 ── Initial Classification    Accounts × Opportunities → 7 priority rules
    ↓
Step 2 ── Invoice Enrichment        Reclassify 19,750 flagged accounts via billing data
    ↓
Step 3 ── Contact & Event Check     Rescue accounts with recent CRM activity (< 3 yrs)
    ↓
Step 4 ── Merge & Consolidation     Union of all passes · dedup · collapse interim statuses
    ↓
Step 5 ── ERP Matching              4-pass reliability-ordered reconciliation (98.8% coverage)
```

### Step 1 — Classification Rules (7 priorities, first match wins)

| Priority | Condition | Status |
|---|---|---|
| P1 | Prospect + target country + created ≤ 2023 | 🟠 Reintegrate |
| P2 | Status = Customer | 🟢 Keep |
| P3 | Old Customer + no opportunity OR inactive 5+ yrs | 🔴 Review |
| P4 | Status = Not Applicable or Prospect (other) | 🔴 Review |
| P5 | No opportunity + account age < 1 yr | 🟡 New client |
| P6 | Last activity > 5 years ago | 🔴 Deactivate |
| P7 | All other (confirmed recent activity) | 🟢 Keep |

### Step 5 — ERP Matching (key innovation)

The CRM fusion code is tested simultaneously against **both** ERP fusion columns, enabling transitive matching through subsidiary relationships:

| Pass | CRM key | ERP key | Matches | Coverage |
|---|---|---|---|---|
| T1 | Account Id | ERP_ID (direct) | 4,175 | 20.0% |
| T2 | Fusion Code | FUSION_CODE_1 **and/or** FUSION_CODE_2 | 16,675 | 79.6% |
| T3 | Fusion Code | FUSION_CODE_2 only | 70 | 0.3% |
| T4 | Account Name | CLIENT_NAME | 38 | 0.2% |

> **Impact:** Bidirectional fusion code matching drove coverage from **8.3% → 98.8%**

---

## 📁 Repository Structure

```
cam-portfolio/
├── notebooks/
│   └── client_database_cleaning.ipynb   # Full annotated pipeline
├── data_samples/
│   └── schema_examples.md               # Anonymized column schemas
├── docs/
│   └── technical_documentation.pdf      # Full technical write-up
├── requirements.txt
└── README.md
```

---

## ⚙️ Setup & Usage

### Requirements

```bash
pip install -r requirements.txt
```

```
pandas>=2.0.0
openpyxl>=3.1.0
```

### Running the notebook

1. Clone the repo
2. Place your source files in a `data/` folder (see `data_samples/` for expected schemas)
3. Update `DATA_DIR` and `OUTPUT_DIR` in the **Imports & Configuration** cell
4. Run all cells sequentially — each step exports an intermediate Excel file

```bash
jupyter notebook notebooks/client_database_cleaning.ipynb
```

### Output

The final file `client_analysis_final.xlsx` contains 9 sheets:

| Sheet | Content |
|---|---|
| Full analysis | All accounts with all enriched columns |
| 🟢 Keep | 6,480 confirmed active clients |
| 🔴 Deactivate | 14,633 accounts with detailed deactivation reasons |
| 🟠 Reintegrate | 97 prospects to re-engage |
| Unmatched ERP | 252 accounts absent from ERP |
| Keep unmatched ERP | 90 active clients not found in ERP — action required |
| Summary | Status count with conditional color coding |
| ERP Matching Summary | 3 blocks: status×ERP crosstab, pass detail, match rate by status |

---

## 🛠️ Technical Choices

**Why pandas `.apply()` for classification?**  
Each account's classification depends on 4–5 interacting conditions (status, country, age, activity dates). A vectorized approach would require complex boolean masking chains that sacrifice readability. The priority-rule structure maps naturally to a function with early returns.

**Why bidirectional fusion code matching?**  
ERP subsidiary hierarchies mean a single CRM account can appear under different fusion codes depending on which entity invoiced it. Testing both columns simultaneously captures these transitive relationships without requiring a pre-join.

**Why 6 separate data sources rather than one join?**  
Each source answers a distinct business question. Invoice history answers "did this client pay us recently?" independently of CRM activity. Treating them separately preserves the diagnostic value of each criterion and makes the classification reasons fully auditable.

---

## 📈 Key Metrics

| Metric | Value |
|---|---|
| Accounts processed | 21,210 |
| Classification accuracy (manual spot-check) | ~97% |
| ERP match coverage | 98.8% |
| Runtime (full pipeline) | < 30 seconds |
| Output file size | ~15 MB (9 sheets, full formatting) |

---

## 👤 Author

**M2 Data and BI Analyst Intern** · May 2026  
Pipeline design, implementation, validation, and documentation.

---

*Built with Python 3.11 · pandas · openpyxl*
