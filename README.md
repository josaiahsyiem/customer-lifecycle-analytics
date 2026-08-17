# Customer Lifecycle Analytics

**Segmentation and retention analysis for a UK online retailer, built with SQL and Python.**

Revenue in this business is dangerously concentrated: roughly a quarter of customers generate about 69% of all revenue, and most first-time buyers never return. This project quantifies that concentration, segments 5,878 customers by lifecycle stage and value, and measures how retention differs across segments over a two-year window, ending in a concrete set of campaign recommendations.

## Key Results

| Question | Answer |
|---|---|
| Who drives revenue? | Champions (~25% of customers) contribute ~69% of revenue |
| Where is growth hiding? | Loyal + Potential Loyalists: ~25% of customers, ~19% of revenue |
| Where is the leak? | ~40% of customers sit in lapsing/churned segments contributing ~10% of revenue |
| When do customers churn? | Steep drop after month 1; retention stabilizes into a long, thin tail |
| Do segments predict retention? | Yes: Champions and Loyal customers retain far longer; At Risk and Hibernating churn rapidly |

## Approach

**1. Data preparation** (Python). Cleaned ~1.07M raw transactions from the UCI Online Retail II dataset down to 779,425 valid purchase records: removed missing customer IDs, cancellations, returns, zero-price rows, and duplicates.

**2. Feature engineering** (SQL Server). Built a customer-level table with lifecycle and value metrics: recency, order frequency, total and average spend, interpurchase gap, active lifespan, basket size, and average unit price.

**3. Segmentation** (Python). Evaluated K-Means on transformed RFM features first. It produced separable value tiers (silhouette ~0.43 at k = 3), but coarse value clusters cannot distinguish a lapsed high-spender from an active one, which is exactly the distinction marketing acts on. Pivoted to a rule-based R x F lifecycle grid producing ten actionable segments, with monetary value used for profiling and a value-tier overlay that prioritizes high-spend customers within each segment.

**4. Retention analysis** (SQL + Python). Assigned customers to monthly cohorts by first purchase, computed retention at the customer-month grain, and joined cohorts with segments to compare engagement longevity: overall retention curves, per-segment curves, cohort heatmaps, and cohort-size-weighted comparisons.

## Recommendations

- Prioritize retention of high-value repeat customers over broad acquisition; the revenue math strongly favors it
- Invest in post-purchase onboarding to attack the steep month-1 drop
- Run win-back campaigns on high-value At Risk and Cannot Lose Them customers (252 priority targets identified)
- Suppress paid targeting for Hibernating customers and reallocate that budget upward

## Repository Structure

```
notebooks/
  01_eda_and_cleaning.ipynb        # data validation and cleaning
  02_customer_segmentation.ipynb   # K-Means evaluation, RFM segmentation, value tiers
  03_retention_analysis.ipynb      # cohort construction and retention analysis
sql/
  feature_engineering.sql          # customer-level feature table
  cohort_queries.sql               # cohort activity tables (overall and by segment)
images/                            # exported visualizations
requirements.txt
```

## Reproducing the Analysis

1. Download the [UCI Online Retail II dataset](https://archive.ics.uci.edu/dataset/502/online+retail+ii) and place `online_retail_II.csv` in `notebooks/`
2. `pip install -r requirements.txt`
3. Run notebook 01 to produce the cleaned transaction table
4. Load it into SQL Server, run the scripts in `sql/`, and export the resulting tables as CSVs into `notebooks/` (pre-generated copies of the derived CSVs are included, so notebooks 02 and 03 also run as-is)
5. Run notebooks 02 and 03 in order

**Stack:** Microsoft SQL Server (T-SQL), Python (pandas, NumPy, scikit-learn, seaborn, matplotlib, plotly)
