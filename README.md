




PL/SQL Window Functions Assignment
---
Name: MUHIMPUNDU Anne‑Marie


ID: 29398

Course: Database Development with PL/SQL
---


Step1: Problem Definition
-
The company is Pharmacie Conseil, a retail pharmacy in Kigali, Rwanda. It has Sales and Marketing departments.


Data Challenge
--
Pharmacie Conseil needs to analyze medication sales across different regions to optimize inventory management and marketing strategies. They are struggling to identify top‑performing products by region, understand sales trends over time, and segment customers effectively for targeted campaigns


Expected Outcome
-
Identify regional product performance, analyze sales growth patterns, and create customer segments for personalized marketing campaigns to increase revenue by 15% in the next quarter.


Step2: Success Criteria
-

Define exactly 5 measurable goals:

1.Top 5 products per region/quarter → RANK()

    WITH regional_products AS (
    SELECT 
        c.region,
        p.name AS product_name,
        SUM(t.amount) AS regional_revenue,
        RANK() OVER (PARTITION BY c.region ORDER BY SUM(t.amount) DESC) AS region_rank
    FROM transactions t
    JOIN customers c ON t.customer_id = c.customer_id
    JOIN products p ON t.product_id = p.product_id
    GROUP BY c.region, p.name)
SELECT region, product_name, regional_revenue, region_rank
FROM regional_products
WHERE region_rank <= 5
ORDER BY region, region_rank;

2.Running monthly sales totals → SUM() OVER()
-
SELECT 
    TO_CHAR(sale_date, 'YYYY-MM') AS sale_month,
    SUM(amount) AS monthly_sales,
    SUM(SUM(amount)) OVER (
        ORDER BY TO_CHAR(sale_date, 'YYYY-MM')
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_total
FROM transactions
GROUP BY TO_CHAR(sale_date, 'YYYY-MM')
ORDER BY sale_month;

Step4: Window Function Implementation
--
1. RANKING
   ---
   Pharmacie Conseil wants to know which customers spend the most money in each region. Ranking functions allow us to assign positions to customers based on their total spending.

ROW_NUMBER() gives each customer a unique sequential number.

RANK() shows position but leaves gaps if there are ties.

DENSE_RANK() shows position without gaps.

PERCENT_RANK() shows relative standing as a percentage.

This helps identify top spenders (Platinum customers) and compare them across regions.
![Image Alt](https://github.com/muhimpunduanne/plsql_window_functions_29398_MUHIMPUNDU-Anne-marie/blob/b46e8f49c1c38c92b743dab93ed7b32dee0b378d/ScreenShoots/Ranking%20Functions.png)

2. Aggregate Window Functions
   --
Explanation:  
Pharmacie Conseil needs to track overall sales trends. Aggregate window functions calculate cumulative totals and moving averages.

SUM() OVER with ROWS or RANGE shows running totals of monthly sales.

AVG() OVER with a frame (like 3 months) smooths fluctuations and highlights long‑term trends.

This helps management see whether revenue is growing steadily or dropping seasonally.

![Image Alt](https://github.com/muhimpunduanne/plsql_window_functions_29398_MUHIMPUNDU-Anne-marie/blob/a7911e1fc7a6549c1fd4aaadbdd453e1874f4352/ScreenShoots/Aggregate%20Window%20Functions.png)















































