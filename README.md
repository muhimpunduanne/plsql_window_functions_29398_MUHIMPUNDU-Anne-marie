




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

Step3: Database Schema
--
Here is the 3 tables that are created

CUSTOMERS
--
![Image Alt](https://github.com/muhimpunduanne/plsql_window_functions_29398_MUHIMPUNDU-Anne-marie/blob/6de7ccad7b5f4f2b2efc980ea2682ab14259cdf0/customertable.png)

products
--
![Image Alt](https://github.com/muhimpunduanne/plsql_window_functions_29398_MUHIMPUNDU-Anne-marie/blob/14c77ca8985548101459e8e15ea6a8b47ea97d21/products%20table.png)

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


3. Navigation Functions
   --
Explanation:  
Pharmacie Conseil wants to compare sales month by month. Navigation functions let us look backward or forward in the data.

LAG() retrieves the previous month’s sales.

LEAD() retrieves the next month’s sales.

By comparing current vs previous, we calculate growth percentage.

This shows whether sales are increasing or decreasing, helping management react quickly.

![Image Alt](https://github.com/muhimpunduanne/plsql_window_functions_29398_MUHIMPUNDU-Anne-marie/blob/4ed27cb8461a2fe95304e7730459d0f472d1ee25/ScreenShoots/Navigation%20Functions.png)


4. Distribution Functions
   --
Explanation:  
Pharmacie Conseil wants to segment customers into groups for marketing. Distribution functions divide customers into quartiles and show their relative position.

NTILE(4) splits customers into four groups (Platinum, Gold, Silver, Bronze).

CUME_DIST() shows how far along the distribution each customer is compared to others.

This helps design loyalty programs and promotions targeted at different spending levels.


![Image Alt](https://github.com/muhimpunduanne/plsql_window_functions_29398_MUHIMPUNDU-Anne-marie/blob/66df80084f476f1fb0ec006ca8d69dc19382c5b3/ScreenShoots/Distribution%20Functions.png)

Step6: Result Analysis
--
Descriptive Analysis
--
Kigali region shows highest medication sales.

Painkillers dominate overall sales volume.

Platinum quartile customers contribute most revenue.

Diagnostic Analysis
--
Kigali’s dominance due to population density and purchasing power.

Seasonal dips in March linked to reduced spending after holidays.

Antibiotics and supplements drive steady revenue.

Prescriptive Analysis
--
Increase stock of top-selling medications in Kigali.

Target Platinum and Gold customers with loyalty programs.

Promote supplements in lower-performing regions.

Launch campaigns for inactive customers identified via LEFT JOIN.

Step7: References
--
PostgreSQL Documentation – Window Functions

PostgreSQL JOINs Guide

"Database System Concepts" by Silberschatz, Korth, Sudarshan

ACM Digital Library – SQL Best Practices






























