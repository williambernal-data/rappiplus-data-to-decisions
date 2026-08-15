# RappiPlus: From Data to Business Decisions

An end-to-end analysis of RappiPlus's business performance — combining Python data cleaning, SQL queries on PostgreSQL, statistical testing, and an executive Power BI dashboard — built to answer whether the business is profitable, where users are lost, and which product decisions are backed by evidence.

## Business Context

RappiPlus needed to evaluate its overall performance to support data-driven business decisions, integrating five sources: orders, product catalog, marketing investment, in-platform user behavior (via SQL), and the results of a checkout A/B experiment.

**Core business questions:**
1. Can we trust the data?
2. Is the business profitable?
3. At which stage are users lost?
4. Do users come back after their first interaction?
5. Do product changes generate a measurable impact?
6. How are these results communicated clearly to the business?

## Key Business Findings

- **The business is profitable**: $9.62M in revenue, $2.92M in profit, and a 30.36% operating margin (on the main analysis, excluding outlier corporate transactions).
- **Marketing spend shows no direct correlation with month-to-month revenue** — spend stays stable while revenue fluctuates independently, a signal that current growth depends on factors beyond paid advertising investment.
- **The biggest funnel leak is at checkout**: 13.29% of users are lost between *Begin Checkout* and *Add Payment Info* — the most critical stage to optimize, above any other step in the funnel.
- **The first week defines retention**: users who survive the first 7 days after registration show stable staying behavior; abandonment is concentrated almost entirely in that initial period.
- **The checkout UI change tested via A/B had no statistically significant impact** on conversion (p = 0.4161) — not recommended for deployment in its current state.
- **10 high-volume corporate orders (81.48% of original gross revenue)** were identified as an outlier pattern and separated from the main analysis to avoid distorting end-consumer behavior KPIs.

## Business Recommendations

- **Prioritize optimizing the payment flow** (*Begin Checkout → Add Payment Info*), the point of greatest user loss in the entire funnel.
- **Invest in onboarding and early activation** during the first 7 days after registration, since it's the critical window that determines whether a user stays or churns.
- **Don't deploy the new checkout design tested in the A/B experiment** — redirect development effort to other design hypotheses or to the real friction points in the funnel.
- **Validate with the business the nature of the 10 excluded high-volume orders**: if they correspond to a legitimate B2B channel, they should be treated as a separate business line with its own KPIs, rather than mixed into the B2C analysis.

## Methodology

**Step 1 — Data quality (Python):** date type validation and conversion, standardization of inconsistent categories, removal of negative and invalid values, consistency checks between amounts, duplicate removal, marketing channel recovery from `id_campaña`, and outlier detection via interquartile range (IQR) — with explicit documentation of each cleaning decision and its quantified impact.

**Step 2 — Profitability (Python):** calculation of business KPIs (revenue, cost, profit, margin), average ticket, units per order, and spend by marketing channel, integrating orders and product catalog.

**Step 3 — Conversion funnel (SQL / PostgreSQL):** CTE-based queries on the `events` table to calculate unique users per stage, conversion rates between steps, and final conversion rate, with an explicit methodological note on open vs. closed funnels and the tracking anomaly detected.

**Step 4 — Cohort retention (SQL / PostgreSQL):** cohorts defined by registration month, with retention calculated at 7, 14, and 21 days via `LEFT JOIN` and conditional aggregations.

**Step 5 — A/B test (Python / statsmodels):** experiment integrity check (Sample Ratio Mismatch), two-proportion Z-test to compare conversion rates between control and treatment variants, with statistical and business interpretation of the p-value.

**Step 6 — Executive dashboard (Power BI):** consolidation of findings into a 3-page report — Executive Overview, Commercial Analysis, and Outlier Transactions (Excluded) — built on the cleaned datasets.

## Repository Structure

```
├── notebooks/
│   └── rappiplus_business_analysis.ipynb
├── data/
│   ├── raw/
│   │   ├── rappiplus_orders_raw.csv
│   │   ├── rappiplus_catalog.csv
│   │   └── rappiplus_marketing_spend.csv
│   ├── orders_clean.csv
│   ├── catalog_clean.csv
│   ├── marketing_clean.csv
│   ├── orders_outliers.csv
│   └── experiment_checkout_ui.csv
├── dashboard/
│   └── rappiplus_dashboard.pbix
├── screenshots/
│   ├── 01_executive_overview.png
│   ├── 02_commercial_analysis.png
│   └── 03_outlier_transactions.png
├── .env.example
├── .gitignore
├── LICENSE
├── requirements.txt
└── README.md
```

## Tech Stack

Python (pandas, SQLAlchemy) · PostgreSQL (SQL, CTEs) · SciPy / statsmodels (proportion tests) · Power BI Desktop · DAX

## Note on Credentials

The database connection uses environment variables loaded from a `.env` file (not included in this repository — see `.env.example` for the template). The `.env` file is excluded via `.gitignore` to avoid exposing credentials.

## Limitations

The conversion funnel implements an open-counting approach (unique users per event, without enforcing a strict sequence), which allowed a real tracking anomaly to be detected between `select_item` and `add_to_cart`, but doesn't measure strict per-session sequential conversion. The 10 high-volume orders were excluded from the main analysis due to their unusual pattern, but their exact nature (capture error vs. legitimate B2B channel) couldn't be confirmed with the available data alone.

## Next Steps

- Redesign the payment flow to reduce the 13.29% leakage detected at *Add Payment Info*
- Design and test a reinforced onboarding program for the first 7 days
- Build and validate a closed (per-session sequential) funnel as a complement to the current open-funnel analysis
- Confirm with the business team the nature of the excluded high-volume orders

---

**Author:** William Andrés Bernal Sosa — [GitHub](https://github.com/williambernal-data)
