# Sales Data Analysis — End-to-End Project

I wanted to build a complete analyst workflow, not just run some aggregations. This project takes 1,000 real sales transactions and turns them into a full pipeline: exploration, cleaning, KPI computation, an interactive dashboard, and two client-ready reports. Every finding is backed by a specific number, and every recommendation points to a specific action.

---

## What I Was Trying to Build

A structured analysis that a business could actually act on. Three questions drove everything:

- **Where is revenue coming from — and where is it leaking?** (margins, discounts, category mix)
- **Who are the customers and how do they behave differently?** (new vs. returning, channel preference)
- **Where are the performance gaps?** (reps, regions, timing)

The output is not a notebook. It is a cleaned dataset, 22 computed KPIs, an interactive HTML dashboard, a KPI breakdown report, and a full project summary — everything a data analyst would deliver to a client.

---

## The Dataset

1,000 sales transactions across one full year (January 2023 – January 2024). Each row is one order and contains:

- Product sold, quantity, unit cost, unit price, sales amount
- Which region, which sales rep, which channel
- Customer type (New / Returning), payment method, discount applied
- Sale date

**14 columns. 4 regions. 4 categories. 5 reps. One year.**

---

## Project Structure
```
project_amit2/
│
├── data/
│   ├── raw/
│   │   └── sales_data.csv
│   └── processed/
│       └── sales_data_clean.csv
│
├── scripts/
│   └── analysis.py
│
├── reports/
│   ├── sales_dashboard.html
│   ├── Sales_Report_2023.docx
│   └── Sales_Project_Report.docx
│
├── venv/
├── .gitignore
├── requirements.txt
└── README.md
```

---

## How to Run
```bash
# 1. Clone or download the project
cd project_amit2

# 2. Create virtual environment
python -m venv venv
venv\Scripts\activate        # Windows
source venv/bin/activate     # Mac / Linux

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run
cd scripts
python analysis.py
```

One script. Cleaned data goes to `data/processed/`. Reports go to `reports/`.

---

## What the Script Does

Built in 3 incremental steps — each one builds on the last.

**Step 1 — Explore**
Load the raw CSV and audit everything: shape, data types, missing values, duplicates, value distributions for every column, numeric ranges, date coverage, and best-seller rankings by revenue and quantity.

**Step 2 — Clean**
One column was dropped (`Region_and_Sales_Rep` — a pure string concat of two existing columns, zero added value). Date parsed as datetime. Five new columns derived: `Gross_Margin_Pct`, `Month`, `Quarter`, `Day_of_Week`, `Discount_Tier`. Invalid rows filtered out. Final dataset: 1,000 clean rows, 18 columns.

**Step 3 — KPIs**
22 KPIs across five domains:

- **Headline numbers** — total revenue, orders, AOV, average discount, gross margin
- **Revenue breakdowns** — by category, region, rep, channel, payment method
- **Customer analysis** — new vs. returning split, AOV by type, channel preference
- **Discount analysis** — correlation with revenue (0.023 — effectively zero), tier buckets, rep-level comparison
- **Time & demand** — monthly trend, quarterly, day-of-week performance

One specific finding worth highlighting: the discount–revenue correlation. At 0.023, it is statistically indistinguishable from zero. The business is giving away ~$763K in margin annually with no measurable return in order value.

---

## Key Numbers

| Metric | Value |
|--------|-------|
| Total Revenue | $5,019,265 |
| Total Orders | 1,000 |
| Avg Order Value | $5,019 |
| Avg Discount | 15.2% |
| Avg Gross Margin | 14.3% |
| New Customers | 504 (50.4%) |
| Returning Customers | 496 (49.6%) |
| Returning Customer AOV Premium | +$94 vs New |
| Best Month | January — $495,420 |
| Best Day | Thursday — $808,000 |

---

## What the Data Actually Revealed

**Discounts are not working.**
A correlation of 0.023 between discount rate and sales amount means the current policy has no commercial justification. Orders with 0% discount and orders with 30% discount generate the same revenue on average. The estimated annual cost of this policy is ~$763K in foregone margin.

**Clothing earns the most but keeps the least.**
It is the top revenue category at $1.31M but carries the lowest gross margin at 13.8% — below the 14.3% average. Every percentage point of margin improvement in Clothing alone is worth ~$13K. The category is being underpriced or over-discounted.

**Returning customers are worth more — and currently equal to new.**
The 50/50 split between new and returning customers is healthy, but returning customers spend $94 more per order. Shifting that ratio to 60/40 in favour of returning would add an estimated $235K in annual revenue without increasing acquisition spend.

**Thursday and Friday drive a third of annual revenue.**
Those two days generate $1.59M — 31.7% of the total. Wednesday and Saturday are consistently the weakest, running 11–14% below average. Most teams treat every day identically. The data says they should not.

**One rep is 33% behind — and in the weakest region.**
Charlie generates $281K less than David, has the fewest orders (169 vs 222), and is disproportionately active in South — the lowest-performing region. Whether the problem is rep-driven or market-driven is the key question, and the answer determines whether the fix is coaching or territory restructuring.

---

## Challenges

**Category vs. Product ID.**
Product_ID spans multiple categories — the same ID appears under Clothing, Electronics, and Food. This made product-level analysis unreliable for category attribution. The fix was to use Product_ID only for ranking (best sellers by revenue and quantity) and rely on Product_Category for all margin and revenue segmentation.

**Discount analysis needed more than a correlation.**
The 0.023 correlation was the headline, but it needed context. Breaking the data into three discount tiers (Low / Mid / High) showed that high-discount orders do have a marginally higher AOV ($5,139 vs $4,949) — but the $190 uplift does not come close to offsetting the margin cost of a 25% average discount. That is the argument that makes the recommendation actionable.

**Sales_Amount does not equal Unit_Price × Quantity × (1 − Discount).**
These columns do not reconcile. The difference is sometimes in the hundreds. Rather than trying to reverse-engineer the discrepancy, Sales_Amount was treated as the ground truth for revenue, and Unit_Price/Unit_Cost were used exclusively for margin calculations. Both are internally consistent — they just represent different things.

---

## Stack
```
Python 3       — core language
Pandas         — data manipulation and KPI calculation
Chart.js       — interactive HTML dashboard
Node.js        — Word document generation
docx (npm)     — report formatting and layout
```

---

## Outputs

| File | Description |
|------|-------------|
| `sales_data_clean.csv` | Cleaned 18-column dataset |
| `sales_dashboard.html` | Interactive dark-themed dashboard — open in any browser |
| `Sales_Report_2023.docx` | KPI-by-KPI breakdown with findings and actions |
| `Sales_Project_Report.docx` | Full project summary — what we did, found, and recommend |
