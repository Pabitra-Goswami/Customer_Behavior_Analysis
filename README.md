# Customer Shopping Behavior Analysis

An end-to-end data analytics project that takes a raw retail customer dataset through **Python** (cleaning and feature engineering), **MySQL** (business analysis with SQL) and **Power BI** (interactive dashboard).

## Table of Contents
- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Repository Structure](#repository-structure)
- [Dataset](#dataset)
- [Workflow](#workflow)
- [SQL Analysis](#sql-analysis)
- [Dashboard](#dashboard)
- [Key Insights](#key-insights)
- [Recommendations](#recommendations)
- [How to Run](#how-to-run)
- [Limitations and Future Work](#limitations-and-future-work)
- [Author](#author)

## Overview
The goal is to understand who buys, what they buy and what drives higher spend, using 3,900 customer transactions (about **$233K** in total revenue, average purchase **$59.76**, average rating **3.75**). The project answers ten business questions on revenue, discounts, subscriptions, shipping and customer loyalty, and presents the results in a Power BI dashboard.

## Tech Stack
| Area | Tools |
|---|---|
| Data cleaning and feature engineering | Python, pandas, Jupyter Notebook |
| Database | MySQL, SQLAlchemy, PyMySQL |
| Analysis | SQL (aggregations, subqueries, CTEs, window functions) |
| Visualisation | Power BI |

## Repository Structure
```
.
├── customer_shopping_behavior.csv        # Raw dataset (3,900 rows, 18 columns)
├── customer_trends_data_analysis.ipynb   # Cleaning, feature engineering, load to MySQL
├── customer_behavior_sql_queries.sql     # 10 business questions in SQL
├── Customer_Behavior_Dashboard.pbix      # Power BI dashboard
├── Project_Report.docx                   # Full project report
└── README.md
```

## Dataset
Each row is one customer purchase. Main fields:

| Group | Columns |
|---|---|
| Customer | Customer ID, Age, Gender, Location |
| Product | Item Purchased, Category, Size, Color, Season |
| Transaction | Purchase Amount (USD), Review Rating, Payment Method, Shipping Type |
| Behaviour | Subscription Status, Discount Applied, Promo Code Used, Previous Purchases, Frequency of Purchases |

The dataset has no date column, so time-series trends are not covered.

## Workflow
1. **Load and explore** the CSV with pandas.
2. **Clean**
   - 37 missing `Review Rating` values filled with the median rating of the same category.
   - `Promo Code Used` dropped because it is identical to `Discount Applied` in every row.
   - Column names standardised to `snake_case` (`Purchase Amount (USD)` becomes `purchase_amount`).
3. **Engineer features**
   - `age_group`: quartile bins, Young Adult (18–31), Adult (32–44), Middle-aged (45–57), Senior (58–70).
   - `purchase_frequency_days`: purchase frequency converted to days (for example Weekly = 7, Monthly = 30, Annually = 365).
4. **Load into MySQL** (`customer_behavior` database, `customer` table) with SQLAlchemy and PyMySQL.
5. **Analyse with SQL** (see below).
6. **Visualise in Power BI**.

## SQL Analysis
All queries are in [`customer_behavior_sql_queries.sql`](customer_behavior_sql_queries.sql).

| # | Question | Result |
|---|---|---|
| Q1 | Revenue by gender | Male $157,890 vs Female $75,191 |
| Q2 | Discounted purchases above the average amount | 839 customers |
| Q3 | Top 5 products by average rating | Gloves 3.86, Sandals 3.84, Boots 3.82, Hat 3.80, then Skirt / T-shirt tied at 3.78 |
| Q4 | Standard vs Express average spend | Express $60.48 vs Standard $58.46 |
| Q5 | Subscribers vs non-subscribers | Avg $59.49 vs $59.87; revenue $62,645 vs $170,436 |
| Q6 | Top 5 products by discount rate | Hat 50.0%, Sneakers 49.7%, Coat 49.1%, Sweater 48.2%, Pants 47.4% |
| Q7 | Customer segments (New / Returning / Loyal) | 83 / 701 / 3,116 |
| Q8 | Top 3 products per category | Jewelry, Pants, Sandals, Jacket lead their categories |
| Q9 | Are repeat buyers more likely to subscribe? | 27.6% of repeat buyers subscribe vs 27.0% overall |
| Q10 | Revenue by age group | Young Adult $62,143 leads; all groups within 24–27% |

## Dashboard
`Customer_Behavior_Dashboard.pbix` is a single-page interactive report with:
- **KPI cards:** Number of Customers, Average Purchase Amount, Average Review Rating
- **Charts:** Sales and Revenue by Category, Sales and Revenue by Age Group, subscription status donut
- **Slicers:** Gender, Category, Subscription Status, Shipping Type

<!-- Add a screenshot: ![Dashboard](images/dashboard.png) -->

## Key Insights
- **Revenue follows customer volume, not spend.** Men generate about 68% of revenue, but only because they are 68% of customers; average spend is nearly equal ($59.54 vs $60.25).
- **Clothing leads** with 44.7% of revenue, followed by Accessories (31.8%), Footwear (15.5%) and Outerwear (8.0%).
- **Subscribers do not spend more** ($59.49 vs $59.87 average), and only 27% of customers subscribe.
- **Discounts do not lift spend.** 43% of purchases use a discount, yet they average $59.28 vs $60.13 without one.
- **Customers are highly loyal:** about 80% have more than 10 previous purchases.
- **Age groups contribute almost equally** to revenue.

## Recommendations
- Redesign the subscription offer (for example free Express shipping) and target repeat buyers who have not subscribed.
- Test reducing discounts on products already discounted about half the time.
- Run acquisition campaigns aimed at women, who spend as much per purchase as men but are a third of customers.
- Prioritise Clothing and Accessories, and feature high-rated items such as Gloves, Sandals and Boots.

## How to Run
**Prerequisites:** Python 3.9+, MySQL Server, Power BI Desktop (Windows).

1. Clone the repository
   ```bash
   git clone https://github.com/<your-username>/<your-repo>.git
   cd <your-repo>
   ```
2. Install dependencies
   ```bash
   pip install pandas sqlalchemy pymysql jupyter
   ```
3. Create the database in MySQL
   ```sql
   CREATE DATABASE customer_behavior;
   ```
4. Set your MySQL credentials as environment variables (do not hard-code them)
   ```bash
   export DB_USER=root
   export DB_PASSWORD=your_password
   ```
   and in the notebook's connection cell use:
   ```python
   import os
   engine = create_engine(
       f"mysql+pymysql://{os.environ['DB_USER']}:{os.environ['DB_PASSWORD']}@localhost:3306/customer_behavior"
   )
   ```
5. Run `customer_trends_data_analysis.ipynb` to clean the data and create the `customer` table.
6. Run the queries in `customer_behavior_sql_queries.sql` in MySQL Workbench or the CLI.
7. Open `Customer_Behavior_Dashboard.pbix` in Power BI Desktop and refresh the data source if prompted.

## Limitations and Future Work
- No date field, so no trend or cohort analysis.
- Group differences are small and no significance tests were run; treat findings as descriptive.
- The "Loyal" segment (11+ purchases) covers about 80% of customers and could be split further.
- Next steps: statistical testing, RFM or clustering segmentation, a model to predict subscription or high spend.

## Author
**Pabitra Goswami**
[LinkedIn](https://www.linkedin.com/in/pabitra-goswami) · [GitHub](https://github.com/Pabitra-Goswami)
