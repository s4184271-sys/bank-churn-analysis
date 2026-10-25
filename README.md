# Bank Customer Churn Analysis

**Which customer behaviours and attributes consistently signal churn, and what early-warning thresholds should the business monitor?**

A data analyst portfolio project that analyses 10,000 bank customers with **pandas, matplotlib/seaborn and Power BI**. There is **no machine-learning model**: every finding comes from exploratory analysis and a transparent, rule-based risk score that anyone on a business team can read and audit.

---

## Dashboard

| Overview | Drivers | Risk Watchlist |
|---|---|---|
| [![Overview](images/overview.png)](images/overview.png) | [![Drivers](images/drivers.png)](images/drivers.png) | [![Risk Watchlist](images/risk_watchlist.png)](images/risk_watchlist.png) |

Click an image to open it full size, or [browse the images folder](images/).

Each page answers one business question:

1. **Overview:** How big is the churn problem and where is it concentrated?
2. **Drivers:** Which attributes drive churn, and which do not?
3. **Risk Watchlist:** Who should we watch first, and what triggers an alert?

---

## Key findings

- **20.4%** of customers churned (2,037 of 10,000).
- **Age is the strongest signal.** Churn is 34.0% at ages 41-50 and **56.2% at ages 51-60**, against 7.5% at ages 18-30.
- **Germany churns about twice as much** as France or Spain (32.4% vs 16.2% and 16.7%). German customers aged 51-60 reach **69.5%**.
- **Number of products matters.** Customers with 2 products are the safest (7.6%). One product is 27.7%, and 3-4 products are 82.7-100% (a small group).
- **Inactive members churn at 26.9%**, against 14.3% for active members.
- **Women churn more than men** (25.1% vs 16.5%).
- **Credit score, salary and tenure do not separate leavers from stayers.** Churn stays between about 18.6% and 22.6% in every band.
- **Concentration:** single-product customers are 51% of the base but 69% of churners; inactive members are 48% but 64%; ages 41-60 are 31% but 61%; Germany is 25% but 40%.

### Early-warning risk score

A rule-based score of 0 to 11 flags customers to watch. Points are added for each signal:

| Signal | Points |
|---|---|
| Age 51-60 | +3 |
| Age 41-50 | +2 |
| 3-4 products | +3 |
| 1 product | +1 |
| Germany | +2 |
| Inactive member | +2 |
| Germany and age 41-60 | +1 |

| Tier | Score | Customers | Churn rate |
|---|---|---|---|
| Low | 0-2 | 4,774 | 5.6% |
| Medium | 3-4 | 3,044 | 19.0% |
| **High** | **5+** | **2,182 (21.8%)** | **54.5%** |

The **High tier holds 58.4% of all churners** (1,190 of 2,037) and **227.1M** of customer balances ("balance at risk").

---

## Business problems and recommended solutions

| Problem found | Evidence | Recommended solution |
|---|---|---|
| Middle-aged customers leave at very high rates | 51-60: 56.2%, 41-50: 34.0% | Run a retention programme for customers aged 41-60: relationship-manager calls, loyalty offers, and a review of products for this age group |
| Germany loses far more customers | 32.4% vs about 16% in France and Spain; German 51-60: 69.5% | Investigate the German market first (pricing, service, competitors) and target German customers aged 41-60 with priority outreach |
| Single-product customers make up most churners | 51% of customers but 69% of churners; 27.7% churn | Cross-sell a second product. Customers with 2 products churn at only 7.6% |
| Inactive members are an early warning | 26.9% churn vs 14.3% for active; 64% of churners are inactive | Set an inactivity trigger and run a re-engagement campaign before the customer leaves |
| Customers with 3-4 products churn almost completely | 82.7% and 100% (small group) | Review the 3-4 product bundles for problems such as fees or complexity |
| Risk is concentrated in a small group | High tier is 21.8% of customers but holds 58.4% of churners and 227.1M in balances | Focus the retention budget on the High tier first, using the watchlist, and monitor the alert triggers monthly |
| Credit score, salary and tenure do not predict churn | Churn stays between 18.6% and 22.6% in every band | Do not target retention by credit score, salary or tenure; spend the effort on the factors above |

**Early-warning thresholds to monitor:** risk score of 5 or more, age 41-60, Germany, one product, or an inactive member.

---

## KPI cards and metrics used

### KPI cards

| Page | KPI card | What it shows |
|---|---|---|
| Overview | Total Customers | 10,000 customers in the dataset |
| Overview | Churned Customers | 2,037 customers who left |
| Overview | Churn Rate | 20.4% of customers churned |
| Overview and Risk Watchlist | Balance_at_Risk | 227.1M held by High-risk customers |
| Risk Watchlist | High Risk Customers | 2,182 customers in the High tier |
| Risk Watchlist | Churners Caught % | 58.4% of all churners are in the High tier |
| Risk Watchlist | High Tier Churn Rate | 54.5% of High-tier customers churned |

### DAX measures

```dax
Total Customers      = COUNTROWS(bank_churn_clean)
Churned Customers    = CALCULATE(COUNTROWS(bank_churn_clean), bank_churn_clean[Exited] = 1)
Retained Customers   = [Total Customers] - [Churned Customers]
Churn Rate           = DIVIDE([Churned Customers], [Total Customers])
Overall Churn Rate   = CALCULATE([Churn Rate], ALL(bank_churn_clean))   -- 20.4% reference line
Balance_at_Risk      = CALCULATE(SUM(bank_churn_clean[Balance]), bank_churn_clean[Risk Tier] = "High")
High Risk Customers  = CALCULATE(COUNTROWS(bank_churn_clean), bank_churn_clean[Risk Tier] = "High")
High Risk Churners   = CALCULATE(COUNTROWS(bank_churn_clean), bank_churn_clean[Risk Tier] = "High", bank_churn_clean[Exited] = 1)
High Tier Churn Rate = DIVIDE([High Risk Churners], [High Risk Customers])
Churners Caught %    = DIVIDE([High Risk Churners], [Churned Customers])
% of Customers       = DIVIDE([Segment Customers], [Total Customers])
% of Churners        = DIVIDE([Segment Churners], [Churned Customers])
```

### Calculated columns

| Column | Purpose |
|---|---|
| `AgeGroup Label` | Age bands: 18-30, 31-40, 41-50, 51-60, 60+ |
| `Risk Score` | 0-11 points from the rule-based signals |
| `Risk Tier` | Low (0-2), Medium (3-4), High (5+) |
| `CreditBand` | Poor, Fair, Good, Very Good, Excellent |
| `SalaryBand` | Salary quartiles Q1 to Q4 |
| `TenureBand` | 0-1, 2-3, 4-6 and 7-10 years |

### Visuals used

- **Clustered column charts:** churn rate by age group, geography, number of products, gender, member status, credit band, salary band, tenure, risk score and risk tier. Bars are **red above the 20.4% overall rate and blue below**, with a dashed reference line.
- **Matrix with conditional formatting:** churn rate by geography x age group (a colour scale from light to dark red).
- **Clustered bar chart:** share of customers vs share of churners (where churners concentrate).
- **Table:** the High-risk customer watchlist, sorted by risk score, with a colour scale on the score and a red tier badge.
- **Slicers:** Geography, Gender, AgeGroup and Member Status as dropdowns.
- **Key Insights panel:** a one-line answer to the page question plus the supporting statistics.

---

## Tools and techniques

- **Python:** pandas (`groupby`, `pd.cut`, `crosstab`, `pivot_table`), matplotlib, seaborn.
- **Analysis:** churn rate by segment, geography x age heatmap, correlation matrix, stacked retained-vs-churned charts, rule-based weighted risk score.
- **Power BI:** DAX measures and calculated columns, conditional formatting, dropdown slicers, page navigation, a consistent three-page layout.

---

## Dataset

Kaggle **"Churn Modelling"** dataset: 10,000 customers, 14 columns (`CreditScore`, `Geography`, `Gender`, `Age`, `Tenure`, `Balance`, `NumOfProducts`, `HasCrCard`, `IsActiveMember`, `EstimatedSalary`, `Exited`, and identifiers). The target is `Exited` (1 = churned).

Download the CSV from Kaggle and save it as `data/Churn_Modelling.csv`. It is not redistributed in this repository.

---

## How to reproduce

```bash
pip install pandas numpy matplotlib seaborn
python bank_churn_analysis.py
```

The script loads the data, cleans it, produces the charts, builds the risk score, and exports `bank_churn_clean.csv`. Load that file into Power BI to rebuild the dashboard.

Main Power BI measures:

```dax
Churn Rate = DIVIDE([Churned Customers], [Total Customers])
Balance_at_Risk = CALCULATE(SUM(bank_churn_clean[Balance]), bank_churn_clean[Risk Tier] = "High")
```

---

## Repository structure

```
.
├── README.md
├── bank_churn_analysis.py      # EDA, charts, risk score, export
├── data/                       # place Churn_Modelling.csv here
├── bank_churn_clean.csv        # cleaned data with risk score and tier
├── bank_churn_dashboard.pbix   # Power BI report
└── images/                     # dashboard screenshots
```

---

## Limitations

- The data is one snapshot of customers, so findings show **association, not causation**.
- The risk score weights are **hand-set from observed churn rates**, not fitted by a model. They are easy to explain but are not optimised.
- Very high scores (9-11) rest on few customers (96, 23 and 16), so their churn rates are less stable.
- Customers with 3-4 products are a small group, so those rates should be read with care.

---

## Author

**Syed Mohammed Ghouse**
[LinkedIn](https://www.linkedin.com/in/your-profile-url) · [Email](mailto:syedmohammed.ghouse23@gmail.com)
