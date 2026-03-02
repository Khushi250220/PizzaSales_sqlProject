Pizza Sales Analysis Project (SQL)
📌 Project Overview

This project analyzes a pizza sales dataset using SQL to extract business insights.
The analysis is divided into Basic, Intermediate, and Advanced levels to demonstrate SQL proficiency including aggregations, joins, grouping, window functions, and revenue analysis.

🗂️ Database Schema

The dataset consists of the following tables:
orders – Contains order date and time information
order_details – Contains quantity of each pizza per order
pizzas – Contains pizza size and price
pizza_types – Contains pizza name and category

🟢 Basic Level Analysis
1️⃣ Retrieve the total number of orders placed

SELECT COUNT(order_id) AS total_orders 
FROM orders;
---

2️⃣ Calculate the total revenue generated from pizza sales

SELECT SUM(od.quantity * p.price) AS total_revenue
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id;
---

3️⃣ Identify the highest-priced pizza
SELECT pizza_id, price
FROM pizzas
ORDER BY price DESC
LIMIT 1;
---

4️⃣ Identify the most common pizza size ordered

SELECT p.size, SUM(od.quantity) AS total_quantity
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
GROUP BY p.size
ORDER BY total_quantity DESC
LIMIT 1;
---

5️⃣ List the top 5 most ordered pizza types along with their quantities

SELECT pt.name, SUM(od.quantity) AS total_quantity
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY total_quantity DESC
LIMIT 5;

---

🟡 Intermediate Level Analysis

6️⃣ Total quantity of each pizza category ordered

SELECT pt.category, SUM(od.quantity) AS total_quantity
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category
ORDER BY total_quantity DESC;
---

7️⃣ Distribution of orders by hour of the day

SELECT HOUR(order_time) AS order_hour,
       COUNT(order_id) AS total_orders
FROM orders
GROUP BY order_hour
ORDER BY order_hour;
---

8️⃣ Category-wise distribution of pizzas

SELECT pt.category,
       COUNT(DISTINCT p.pizza_id) AS total_pizza_types
FROM pizzas p
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category;
---

9️⃣ Average number of pizzas ordered per day

SELECT AVG(daily_total) AS avg_pizzas_per_day
FROM (
    SELECT o.order_date,
           SUM(od.quantity) AS daily_total
    FROM orders o
    JOIN order_details od ON o.order_id = od.order_id
    GROUP BY o.order_date
) AS daily_orders;
---

🔟 Top 3 most ordered pizza types based on revenue

SELECT pt.name,
       SUM(od.quantity * p.price) AS total_revenue
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY total_revenue DESC
LIMIT 3;
---

🔴 Advanced Level Analysis
---

1️⃣1️⃣ Percentage contribution of each pizza type to total revenue

SELECT pt.name,
       SUM(od.quantity * p.price) AS revenue,
       ROUND(
           SUM(od.quantity * p.price) * 100.0 /
           (SELECT SUM(od.quantity * p.price)
            FROM order_details od
            JOIN pizzas p ON od.pizza_id = p.pizza_id),
       2) AS revenue_percentage
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY revenue DESC;
---

1️⃣2️⃣ Cumulative revenue generated over time

SELECT o.order_date,
       SUM(od.quantity * p.price) AS daily_revenue,
       SUM(SUM(od.quantity * p.price)) 
       OVER (ORDER BY o.order_date) AS cumulative_revenue
FROM orders o
JOIN order_details od ON o.order_id = od.order_id
JOIN pizzas p ON od.pizza_id = p.pizza_id
GROUP BY o.order_date
ORDER BY o.order_date;
---

1️⃣3️⃣ Top 3 most ordered pizza types based on revenue for each category

SELECT *
FROM (
    SELECT pt.category,
           pt.name,
           SUM(od.quantity * p.price) AS revenue,
           RANK() OVER (PARTITION BY pt.category
                        ORDER BY SUM(od.quantity * p.price) DESC) AS rank_position
    FROM order_details od
    JOIN pizzas p ON od.pizza_id = p.pizza_id
    JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
    GROUP BY pt.category, pt.name
) ranked
WHERE rank_position <= 3;
---

📊 Key Insights

Identified highest revenue-generating pizza types
Analyzed sales performance by category
Determined peak order hours
Calculated daily and cumulative revenue trends
Found top-performing pizza types within each category
---
🛠️ Tools & Technologies Used

SQL (MySQL / PostgreSQL compatible)
Joins
Aggregation Functions
Window Functions
Subqueries
Group By & Ranking Functions
