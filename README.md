# E-Commerce Sales Analysis (SQL)

Analysis of around 100,000 orders from a Brazilian online marketplace, using SQL to answer six business questions about growth, revenue, customers, reviews, payments and delivery performance.

## Summary of Key Findings

* **Late deliveries hurt customer satisfaction.** About 8% of orders arrived late, and these averaged 2.57 stars compared with 4.29 for on time orders.
* **The business more than doubled in a year.** Orders from January to August rose from 22,968 in 2017 to 53,991 in 2018, an increase of about 135%.
* **Black Friday drove the busiest month.** 24 November 2017 had 1,176 orders, more than double any other day that month.
* **Health and beauty earned the most revenue**, at R$1.26 million.
* **Sales depend heavily on one region.** São Paulo accounts for about 42% of all orders.

## Data

* **Source:** [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) on Kaggle
* **Period:** September 2016 to October 2018
* **Size:** 99,441 orders across 7 related tables (orders, customers, items, products, payments, reviews, category translation)
* **Currency:** Brazilian reais (R$)

The raw data is not included in this repository because of its size. It can be downloaded from the Kaggle link above.

## Tools and Skills

* **Tool:** SQLite (DB Browser for SQLite)
* **SQL:** SELECT, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT, joins across up to four tables, aggregate functions (COUNT, SUM, AVG), ROUND, BETWEEN, CASE WHEN, date functions (strftime)
* **Database setup:** CREATE TABLE AS, ALTER TABLE, DROP TABLE, SELECT DISTINCT

## Files

* `ecommerce_analysis.sql`: all queries, commented and grouped by question
* `README.md`: this summary of the method and findings

## Process

1. Imported seven CSV files into a SQLite database and renamed the tables.
2. Checked the data before analysis: counted rows, looked for missing values and compared order status with delivery dates.
3. Fixed a data quality problem found during checks (duplicated rows in the items table).
4. Wrote one query per business question, testing theories where results suggested a cause.
5. Recorded findings and limitations.

## Data Quality Checks

* **Duplicated table:** revenue totals were initially exactly double the expected values. Row counts showed the items table had been imported twice (225,300 rows instead of 112,650). Duplicates were removed with SELECT DISTINCT and all totals rechecked.
* **Status and date mismatches:** 8 orders were marked as delivered but had no delivery date, and 6 orders not marked as delivered had a delivery date recorded.
* **Undefined payments:** 3 payments had no payment type and a value of 0.
* **Incomplete months:** September 2016 and September to October 2018 had very few orders because they fall at the edges of the dataset, so they were treated as incomplete.

## Questions and Findings

### Q1: Is the business growing?

| Year (January to August) | Orders |
|---|---|
| 2017 | 22,968 |
| 2018 | 53,991 |

Orders grew rapidly through 2017 and levelled off at a higher monthly volume in 2018. Comparing the same months in each year, orders rose by about 135%.

Orders peaked in November 2017. Daily data showed this was driven by Black Friday:

| Date | Orders |
|---|---|
| 2017-11-24 (Black Friday) | 1,176 |
| 2017-11-25 | 499 |
| 2017-11-27 | 403 |
| 2017-11-26 | 391 |
| 2017-11-28 | 380 |

### Q2: Which product categories bring in the most revenue?

| Category | Revenue (R$) |
|---|---|
| health_beauty | 1,258,681.34 |
| watches_gifts | 1,205,005.68 |
| bed_bath_table | 1,036,988.68 |
| sports_leisure | 988,048.97 |
| computers_accessories | 911,954.32 |
| furniture_decor | 729,762.49 |
| cool_stuff | 635,290.85 |
| housewares | 632,248.66 |
| auto | 592,720.11 |
| garden_tools | 485,256.46 |

Revenue was calculated from item prices, excluding freight. Payments could not be used because one payment can cover items from several categories.

### Q3: Which states have the most orders?

| State | Orders |
|---|---|
| SP (São Paulo) | 41,746 |
| RJ (Rio de Janeiro) | 12,852 |
| MG (Minas Gerais) | 11,635 |
| RS | 5,466 |
| PR | 5,045 |

São Paulo alone accounts for about 42% of all orders, and the top three states account for about two thirds.

### Q4: Which categories have the lowest review scores?

Categories with at least 100 reviewed items:

| Category | Average score | Reviewed items |
|---|---|---|
| office_furniture | 3.49 | 1,687 |
| fashion_male_clothing | 3.64 | 131 |
| fixed_telephony | 3.68 | 262 |
| audio | 3.83 | 361 |
| home_confort | 3.83 | 435 |

Office furniture scored 0.60 below the overall average of 4.09. Several of the lowest rated categories are large or bulky items, which may relate to delivery problems.

### Q5: How do customers pay?

| Payment type | Payments | Average payment (R$) |
|---|---|---|
| credit_card | 76,795 | 163.32 |
| boleto | 19,784 | 145.03 |
| voucher | 5,775 | 65.70 |
| debit_card | 1,529 | 142.57 |

Credit card was used for about 74% of payments. Boleto, a Brazilian payment slip paid in cash, was second. Vouchers had a much lower average, likely because orders are often split across several vouchers.

### Q6: Do late deliveries get lower reviews?

| Delivery status | Orders | Average score |
|---|---|---|
| Late | 7,700 | 2.57 |
| On time | 88,653 | 4.29 |

About 8% of delivered orders arrived after the estimated date, and these scored 1.72 points lower on average. Delivery performance is strongly linked to customer satisfaction.

## Limitations

* Reviews are recorded per order, not per item. In Q4, orders with several items have their review counted once per item, so the results show broad patterns rather than exact product ratings.
* The link between bulky categories and late delivery was not tested directly.
* Revenue excludes freight charges.

## Next Steps

* Test whether bulky categories have higher late delivery rates.
* Measure average delivery time by state.
* Build charts and a dashboard of the key findings.
