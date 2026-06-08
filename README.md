# Decoding Customer Value: A SQL-Driven Retention Strategy

> A full-stack data consulting project for a D2C fashion brand — from raw transactional data to a founder-ready intelligence dashboard.

---

## Project Overview

A direct-to-consumer (D2C) fashion brand with ~3,900 customers needed to move beyond surface-level sales numbers. The brand could not answer who its loyal customers were, how much revenue depended on discounts, or which geographies had untapped organic demand.

This project builds the intelligence layer on top of raw behavioral data — using Python, SQL, and Power BI — to answer those questions with evidence, not gut feel.

**Core Question:**
> Is the business successfully building a loyal customer base, or is it reliant on continuous promotional activity? What strategic actions should be taken under either scenario?

---

## Dataset

| Property | Detail |
|---|---|
| Source | Proprietary D2C brand transaction data |
| Customers | ~3,900 |
| File | `Dataset.csv` |
| Key columns | Age, Gender, Category, Purchase Amount, Season, Review Rating, Subscription Status, Discount Applied, Promo Code Used, Previous Purchases, Frequency of Purchases, Location, Payment Method |

**Important constraint:** No loyalty score, no churn label, no timestamps. Every metric must be constructed from available variables.

---

## Project Structure

```
sql/
├── Dataset.csv                  ← Original raw dataset
├── Dataset_Cleaned.csv          ← Cleaned + feature-engineered dataset (Phase 1 output)
├── q1_value_tiers.csv           ← SQL result: high vs low value customers
├── q2_segments.csv              ← SQL result: loyal vs promo-driven segments
├── q3_season_category.csv       ← SQL result: season & category vs tenure
├── q4_geography.csv             ← SQL result: geographic opportunity map
└── q5_ideal_customer.csv        ← SQL result: ideal customer profile
```

---

## Deliverables

| # | Deliverable | Tool | Description |
|---|---|---|---|
| 1 | Cleaned dataset + engineered features | Python | Dependency score, value tier, satisfaction flag |
| 2 | Segmentation queries | SQL | Answers all 5 key business questions |
| 3 | Founder dashboard | Power BI | Four-panel interactive dashboard |
| 4 | Retention playbook | Document | Promo sunset plan + ideal customer profile |
| 5 | Executive summary | Document | Max 1-page findings and recommendations |

---

## Phase 1 — Data Preparation & Feature Engineering (Python / Google Colab)

### Engineered Features

| Feature | Logic | What It Captures |
|---|---|---|
| `Frequency Score` | Text → numeric (Weekly=7 … Annually=1) | Purchase cadence |
| `Loyalty Score` | Previous Purchases (50%) + Frequency (30%) + Subscription (20%) | Composite loyalty signal 0–100 |
| `Promo Dependency Score` | Discount Applied + Promo Code Used (0–2) | Reliance on discounts |
| `Lifetime Value Proxy` | Purchase Amount × Previous Purchases | Revenue potential |
| `Value Tier` | Quartile cut of Lifetime Value Proxy | Low / Mid / High / Premium |
| `Satisfaction Flag` | Review Rating ≥ 4.0 → 1, else 0 | Happy customer indicator |

### How to Run

1. Open [Google Colab](https://colab.research.google.com)
2. Mount Google Drive: `drive.mount('/content/drive')`
3. Load dataset: `pd.read_csv('/content/drive/MyDrive/sql/Dataset.csv')`
4. Run all 11 cells in order
5. Output saved to `/content/drive/MyDrive/sql/Dataset_Cleaned.csv`

---

## Phase 2 — Customer Segmentation & Analysis (SQL / SQLite)

### 5 Key Business Questions

**Q1 — Value Tiers**
> What separates high-value customers from low-value ones?

Segments customers into Premium / High / Mid / Low tiers based on lifetime value proxy. Compares avg spend, loyalty, promo dependency, and satisfaction across tiers.

**Q2 — Loyal vs Promo-Driven**
> Who are genuinely loyal vs those who only buy with a discount?

Classifies customers into 4 segments:
- `Genuinely Loyal` — low promo dependency + high loyalty score
- `Loyal but Promo Reliant` — high loyalty + still uses promos
- `Promo Hunter` — low loyalty + high promo dependency
- `Occasional Buyer` — everything else

**Q3 — Season & Category vs Tenure**
> Which seasons and categories attract new vs returning customers?

Maps product categories and seasons to customer tenure groups (New / Mid / High), identifying entry-point vs retention categories.

**Q4 — Geographic Opportunity**
> Which states show organic demand vs discount-driven volume?

Ranks locations by avg spend and promo dependency, labeling each as:
- `Organic Hotspot` — high spend + low promo dependency
- `Discount Driven` — high promo dependency
- `Mixed` — everything else

**Q5 — Ideal Customer Profile**
> What does the brand's best customer actually look like?

Filters Premium-tier customers and profiles them by age group, gender, payment method, and subscription status.

### How to Run

SQLite is built into Python — no installation needed.

```python
import sqlite3
conn = sqlite3.connect('customer_analysis.db')
df.to_sql('customers', conn, if_exists='replace', index=False)
```

All 5 query results are saved as CSVs to the `sql/` folder in Google Drive.

---

## Phase 3 — Founder Dashboard (Power BI)

### 4-Panel Layout

```
┌─────────────────┬─────────────────┐
│  Customer       │  Promo          │
│  Pyramid        │  Dependency     │
│  (Funnel Chart) │  (Donut + Bar)  │
├─────────────────┼─────────────────┤
│  Geographic     │  Category       │
│  Opportunity    │  Funnel         │
│  (Map)          │  (Stacked Bar)  │
├─────────────────┴─────────────────┤
│  [Season]  [Value Tier]  [Sub]    │
└───────────────────────────────────┘
```

### Panel Details

| Panel | Visual | Source Table | Key Fields |
|---|---|---|---|
| Customer Pyramid | Funnel Chart + KPI Cards | q1_value_tiers | Value Tier, customer_count, avg_spend, avg_loyalty |
| Promo Dependency | Donut Chart + Bar | q2_segments | customer_segment, customer_count, avg_spend |
| Geographic Map | Filled Map | q4_geography | Location, avg_spend, avg_promo_dependency |
| Category Funnel | Stacked Bar + Matrix | q3_season_category | Category, tenure_group, Season, avg_spend |

### Design Tokens

| Property | Value |
|---|---|
| Canvas size | 1280 × 720 |
| Primary color | `#1B2A4A` (dark navy) |
| Accent color | `#00B4D8` (teal) |
| Danger color | `#E63946` (red — promo hunters) |
| Background | `#F8F9FA` (off-white) |
| Font | Segoe UI |

### Slicers
- `Season` (dropdown) — from Dataset_Cleaned
- `Value Tier` (buttons) — from q1_value_tiers
- `Subscription Status` (toggle) — from Dataset_Cleaned

---

## Phase 4 — Retention Playbook

### Promotional Sunset Plan

Identifies which segments to gradually stop discounting, why, and what metrics to track. Every recommendation states the trade-off clearly — not just what to do, but what you risk by doing it.

Structure:
- Segment name
- Trigger behavior
- Rollout timeline
- Success metric

### Ideal Customer Profile

A data-backed description of the brand's most valuable customer type — specific enough for a marketing team to use for targeting decisions today.

---

## Key Questions Addressed

1. Who are the genuinely loyal customers vs those who only buy when there is a discount?
2. What behavioral patterns today predict high customer value over time?
3. Which geographies and demographics are commercially underleveraged?
4. How should the brand restructure its promotional strategy to protect margins without losing volume?
5. What does the brand's ideal customer profile look like, and how can it acquire more of them?

---

## Tech Stack

| Tool | Purpose |
|---|---|
| Python (Google Colab) | Data cleaning + feature engineering |
| pandas, numpy | Data manipulation |
| SQLite (built-in) | SQL query layer |
| Power BI Desktop | Interactive dashboard |
| Google Drive | File storage + collaboration |

---

## Analytical Constraints & Standards

- **No assumed metrics** — every concept (loyalty, dependency, value) is constructed from available variables and justified
- **Two competing loyalty definitions** tested and argued for one based on internal consistency and revenue correlation
- **Every segment label is traceable** — "high-value" maps to a specific combination of variables
- **Every recommendation states trade-offs** — not just what to do, but what you risk

---

*SQL | Consulting and Analytics Project — Decoding Customer Value*
