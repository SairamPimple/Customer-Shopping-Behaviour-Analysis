# Customer Shopping Behaviour Analysis

> **So what:** Spend per order is flat at about $60 in every segment, and 43% of orders carry a discount with no measurable lift in order value. The highest-return next step is a **randomized discount holdout test**, not broader promotions.

<!-- PLACEHOLDER: replace with the updated Tableau screenshot after the polish edits -->
![Customer Behaviour Dashboard](images/Dashboard.png)

**Live dashboard:** [Customer Shopping Behaviour Analysis](https://public.tableau.com/app/profile/sairam.pimple003/viz/CustomerShoppingBehaviourDashboard_17952748241120/CustomerBehaviorDashboard)

![Python](https://img.shields.io/badge/Python-pandas%20%7C%20SciPy%20%7C%20seaborn-blue)
![SQL](https://img.shields.io/badge/PostgreSQL-18%20queries-336791)
![Tableau](https://img.shields.io/badge/Tableau-dashboard-E97627)

## The Business Decision

Spend per order is flat at about $60 across every segment. **Which levers (discounts, subscriptions or product mix) are actually worth investing in?** This project analyses 3,900 retail purchases with Python, PostgreSQL and Tableau to answer that.

## Headline Results

| Revenue | Purchases | Avg. purchase | Avg. rating | Discounted orders | Subscribers |
| --- | --- | --- | --- | --- | --- |
| $233,081 | 3,900 | $59.76 | 3.75 / 5 | 1,677 (43.0%) | 1,053 (27.0%) |

**Flat data is the finding.** Average order value stays within about $57 to $62 across gender, category, age bracket, season, shipping, payment, subscription and discount. Growth has to come from order volume, retention and mix, not basket size.

- **Margin protection:** 43% of orders use a discount with no measurable lift in order value (Welch p = 0.2661). Unless discounts win extra orders, which this data cannot show, margin is being given away.
- **Revenue is concentrated in two categories:** Clothing 44.7% and Accessories 31.8%.
- **The gender gap is volume:** men are 67.7% of revenue on 68.0% of orders; per-order spend is $59.54 (male) vs $60.25 (female).
- **Subscription is not a loyalty lever yet:** subscribers spend $59.49 vs $59.87, and VIP customers subscribe at 27.1%, the same as the 27.0% base rate.

## Approach

1. **Clean (Python):** 37 missing ratings (0.9%) imputed with category medians; snake_case columns; `promo_code_used` dropped (identical to `discount_applied` in every row).
2. **Engineer:** business-defined **age brackets** (18-24, 25-34, 35-54, 55+) and selective **loyalty tiers** (New 1-5, Returning 6-20, Loyal 21-40, VIP 41-50 previous purchases).
3. **Test:** Welch's t-test on discounted vs full-price spend.
4. **Query (PostgreSQL):** 18 business questions using CTEs, window functions, subqueries and `CASE`.
5. **Report (Tableau):** KPIs and filters for gender, subscription, category and shipping type.

## Key Findings

### Revenue and demographics

| Category | Revenue | Orders | Avg. order | Share |
| --- | --- | --- | --- | --- |
| Clothing | $104,264 | 1,737 | $60.03 | 44.7% |
| Accessories | $74,200 | 1,240 | $59.84 | 31.8% |
| Footwear | $36,093 | 599 | $60.26 | 15.5% |
| Outerwear | $18,524 | 324 | $57.17 | 7.9% |

| Age bracket | Orders | Revenue | Avg. order |
| --- | --- | --- | --- |
| 18-24 | 486 | $29,258 | $60.20 |
| 25-34 | 755 | $45,400 | $60.13 |
| 35-54 | 1,481 | $88,833 | $59.98 |
| 55+ | 1,178 | $69,590 | $59.07 |

| Gender | Revenue | Orders | Avg. order |
| --- | --- | --- | --- |
| Male | $157,890 | 2,652 | $59.54 |
| Female | $75,191 | 1,248 | $60.25 |

- Revenue by age follows bracket size (35-54 is the largest group, 38.1% of revenue); **average order does not differ by age** (ANOVA p = 0.6890).
- Men and women buy the same category mix (Clothing about 45% of each gender's orders). Male: $157,890 on 2,652 orders; female: $75,191 on 1,248 orders.
- Seasonality is mild (Spring 999, Fall 975, Winter 971, Summer 955 orders). Clothing leads every season; Pants is the all-season staple.
- **Geography is flat:** the top five of 50 states hold 12.1% of revenue against 10% for an even split.

### Loyalty and subscriptions

| Loyalty tier | Previous purchases | Customers | Share | Subscription rate |
| --- | --- | --- | --- | --- |
| New | 1-5 | 424 | 10.9% | 22.4% |
| Returning | 6-20 | 1,137 | 29.2% | 26.1% |
| Loyal | 21-40 | 1,574 | 40.4% | 28.8% |
| VIP | 41-50 | 765 | 19.6% | 27.1% |

| Group | Customers | Revenue | Avg. spend |
| --- | --- | --- | --- |
| Subscribers | 1,053 (27.0%) | $62,645 | $59.49 |
| Non-subscribers | 2,847 (73.0%) | $170,436 | $59.87 |

- **Repeat buyers (more than 5 previous purchases): 2,518 non-subscribers vs 958 subscribers** (27.6% subscribe).
- Purchase frequency is nearly identical for both groups (13.3% to 15.2% per frequency band).

### Discount effectiveness

| Orders | n | Mean | Median | IQR |
| --- | --- | --- | --- | --- |
| Discount applied | 1,677 | $59.28 | $60.00 | $38 to $80 |
| No discount | 2,223 | $60.13 | $60.00 | $39 to $81 |

- 839 of 1,677 discounted orders (50.0%) exceeded the overall average spend, against 50.6% of full-price orders.
- Most discounted items: Hat 50.00%, Sneakers 49.66%, Coat 49.07%, Sweater 48.17%, Pants 47.37%.
- Discount users have similar history to non-users (25.74 vs 25.06 previous purchases).

### Product quality and fulfillment

- **Top rated:** Gloves 3.86, Sandals 3.84, Boots 3.82, Hat 3.80, Skirt 3.78. **Item ratings do not differ by much**, so gaps of about 0.1 are not a reliable signal.
- **Watch-list (high volume, below-average rating):** Shirt 3.62, Blouse 3.68, Scarf and Shorts 3.71, Pants 3.72, Coat 3.73.
- **Shipping:** all six types average $58.46 to $60.73 and do not differ significantly. Express $60.48 vs Standard $58.46 is not significant.

## Statistical Validation

| Question | Test | Result |
| --- | --- | --- |
| Do discounts change order value? | Welch t-test | t = -1.1122, p = 0.2661 |

**"Not statistically significant" does not prove "no effect".** The data are observational and cover purchasers only; they show discounts do not raise *basket value*, not whether they win *orders*.

## Recommendations

| # | Action | Evidence | 
| --- | --- | --- |
| 1 | **Run a randomized discount holdout test.** Withhold discounts from a random share of eligible orders, starting with the most-discounted items (Hat, Sneakers, Coat, Sweater, Pants). Compare orders per customer and margin per order. | 43% of orders discounted, no lift in order value (p = 0.27) | 
| 2 | **Pilot a frequency-based subscription offer** for the 2,518 unsubscribed repeat buyers, against a control group. | Subscribers do not spend more; VIPs subscribe at the base rate |
| 3 | **Adopt the new loyalty tiers** and treat VIP (41-50 previous purchases, 19.6% of customers) as the selective retention tier. | Old "Loyal" rule covered 79.9% of customers | 
| 4 | **Build an acquisition and engagement plan for women.** | 32.0% of orders, per-order spend equal to men | 
| 5 | **Review Shirt, Blouse and Pants qualitatively** (returns, review text) before acting on ratings. | Top sellers rated below average, but item ratings do not differ beyond chance | 
| 6 | **Do not steer customers to a shipping option to raise basket size.** | No significant shipping differences | 

## SQL Coverage (19 queries)

| Theme | Queries |
| --- | --- |
| Revenue and demographics | Q1 gender revenue, Q10 age-bracket revenue, Q11 category by gender, Q12 age-bracket lift per category, Q14 category revenue share |
| Products and seasons | Q3 top-rated, Q8 top 3 per category, Q13 top 3 per season, Q15 high-volume low-rating, Q18 top category per season |
| Subscriptions and loyalty | Q5 subscribers vs not, Q7 loyalty tiers, Q9 repeat buyers, Q16 frequency by subscription |
| Discounts and fulfillment | Q2 above-average spend by discount status, Q4 all six shipping types, Q6 most discounted items, Q17 previous purchases by discount |

Result screenshots: `[PLACEHOLDER: images/sql_screenshots/SQL_Q1_screenshot ... SQL_Q18_screenshot]`

## Caveats and Limitations

- **Observational, purchaser-only data:** no causal lift is proven, and conversion or incremental volume is invisible. A holdout test is needed.
- **One row per customer:** no time dimension, so trends, retention and true purchase counts cannot be measured.
- **Dataset provenance:** this is a public dataset (`[PLACEHOLDER: dataset name, author, link, license]`) that looks curated or synthetic (for example, previous purchases are spread evenly from 1 to 50). Treat patterns as illustrative, not as a real retailer's Behaviour.
- **Engineering choices matter:** age brackets and loyalty-tier cut-offs are judgment calls; Fortnightly and Bi-Weekly (14 days) and Quarterly and Every 3 Months (90 days) are mapped identically; 37 ratings were imputed.
- **Discount depth is not in the data,** so margin impact is inferred, not measured.

## Repository Structure

Names marked *(placeholder)* are suggestions, not project files.

```text
├── data/customer_shopping_Behaviour.csv
├── notebooks/Customer_Shopping_Behaviour_Analysis.ipynb
├── sql/customer_Behaviour_sql_queries.sql
├── dashboard/Dashboard.png
├── reports/Customer Shopping Behaviour Analysis.docx   (placeholder)
└── images/sql_screenshots/                            (placeholder)
```

## Run It

1. `pip install pandas numpy scipy seaborn matplotlib sqlalchemy psycopg2-binary`
2. Run the notebook; it loads the cleaned data into a `customer` table in a PostgreSQL database named `customer_Behaviour`.
3. Run `sql/customer_Behaviour_sql_queries.sql` against that table.

> Credentials are read from environment variables. Never commit a password.

**Dataset source:** `Kaggel` | **Author:** `[PLACEHOLDER: Sairam Pimple | LinkedIn | Email]`
