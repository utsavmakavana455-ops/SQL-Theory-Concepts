# SQL Theory & Concepts

## 📚 Project Overview

This repository contains my **SQL theory and concept notes** developed alongside practical SQL exercises.

The purpose is to understand not only how to write SQL queries, but also **when and why different SQL concepts are used** in real-world data analysis.

## 🎯 Learning Objectives

* Understand SQL fundamentals
* Learn how to retrieve and filter data
* Understand aggregation and grouping
* Learn subqueries and CTEs
* Understand window functions
* Analyze customer, product, and sales data
* Build a strong foundation for Data Analyst roles

---

# 1. SQL Fundamentals

### SQL

SQL (Structured Query Language) is used to communicate with relational databases.

It can be used to:

* Retrieve data
* Insert data
* Update data
* Delete data
* Analyze data
* Manage database structures

### SELECT

Used to retrieve data from a table.

```sql
SELECT *
FROM orders;
```

### Selecting Specific Columns

```sql
SELECT customer_name, amount
FROM orders;
```

---

# 2. Filtering Data

### WHERE

Used to filter rows based on a condition.

```sql
SELECT *
FROM orders
WHERE amount > 500;
```

### Common Operators

* `=`
* `>`
* `<`
* `>=`
* `<=`
* `<>`
* `AND`
* `OR`
* `IN`
* `BETWEEN`
* `LIKE`

Example:

```sql
SELECT *
FROM orders
WHERE city = 'Berlin'
AND amount > 500;
```

---

# 3. Sorting Data

### ORDER BY

Used to sort query results.

```sql
SELECT *
FROM orders
ORDER BY amount DESC;
```

* `ASC` → Lowest to highest
* `DESC` → Highest to lowest

---

# 4. LIMIT

Used to restrict the number of rows returned.

```sql
SELECT *
FROM orders
ORDER BY amount DESC
LIMIT 5;
```

Useful for finding **Top-N records**.

---

# 5. Aggregate Functions

Aggregate functions perform calculations on multiple rows.

### COUNT()

Counts rows.

```sql
SELECT COUNT(*) AS total_orders
FROM orders;
```

### SUM()

Calculates the total.

```sql
SELECT SUM(amount) AS total_sales
FROM orders;
```

### AVG()

Calculates the average.

```sql
SELECT AVG(amount) AS average_order
FROM orders;
```

### MIN() and MAX()

Find the smallest and largest values.

```sql
SELECT
    MIN(amount) AS minimum_order,
    MAX(amount) AS maximum_order
FROM orders;
```

---

# 6. GROUP BY

Used to group rows with the same value and perform calculations for each group.

```sql
SELECT
    city,
    SUM(amount) AS total_sales
FROM orders
GROUP BY city;
```

Commonly used with:

* `COUNT()`
* `SUM()`
* `AVG()`
* `MIN()`
* `MAX()`

---

# 7. HAVING

Used to filter grouped or aggregated results.

```sql
SELECT
    city,
    SUM(amount) AS total_sales
FROM orders
GROUP BY city
HAVING SUM(amount) > 5000;
```

### WHERE vs HAVING

* `WHERE` filters rows **before grouping**
* `HAVING` filters groups **after aggregation**

---

# 8. CASE WHEN

Used to create conditional logic in SQL.

```sql
SELECT
    order_id,
    amount,
    CASE
        WHEN amount < 200 THEN 'Low'
        WHEN amount <= 500 THEN 'Medium'
        ELSE 'High'
    END AS order_category
FROM orders;
```

Useful for:

* Classification
* Business rules
* Creating categories
* Data segmentation

---

# 9. Subqueries

A subquery is a query inside another query.

Example:

```sql
SELECT *
FROM orders
WHERE amount > (
    SELECT AVG(amount)
    FROM orders
);
```

Subqueries are useful when the result of one query is needed by another query.

---

# 10. Nested Subqueries

A nested subquery contains multiple query levels.

They are useful for more complex analysis such as:

* Above-average customers
* Above-average products
* Ranking aggregated results
* Comparing groups against overall metrics

---

# 11. Common Table Expressions (CTEs)

A CTE creates a temporary named result that can be used by the main query.

Syntax:

```sql
WITH sales_data AS (
    SELECT
        city,
        SUM(amount) AS total_sales
    FROM orders
    GROUP BY city
)
SELECT *
FROM sales_data;
```

CTEs make complex queries easier to read and organize.

---

# 12. Multiple CTEs

Multiple CTEs can be created in the same query.

```sql
WITH sales_data AS (
    SELECT city, SUM(amount) AS total_sales
    FROM orders
    GROUP BY city
),
ranked_data AS (
    SELECT
        city,
        total_sales,
        RANK() OVER (ORDER BY total_sales DESC) AS sales_rank
    FROM sales_data
)
SELECT *
FROM ranked_data;
```

Useful for breaking complex analysis into logical steps.

---

# 13. Window Functions

Window functions perform calculations across related rows without collapsing them into one row.

Common window functions:

* `RANK()`
* `ROW_NUMBER()`
* `LAG()`
* `LEAD()`
* `SUM() OVER()`
* `AVG() OVER()`

---

# 14. RANK()

Assigns a ranking to rows.

```sql
SELECT
    customer_name,
    SUM(amount) AS total_spending,
    RANK() OVER (
        ORDER BY SUM(amount) DESC
    ) AS customer_rank
FROM orders
GROUP BY customer_name;
```

Useful for:

* Top customers
* Sales rankings
* Product rankings

---

# 15. ROW_NUMBER()

Assigns a unique sequential number to each row.

```sql
SELECT
    customer_name,
    amount,
    ROW_NUMBER() OVER (
        ORDER BY amount DESC
    ) AS row_num
FROM orders;
```

Unlike `RANK()`, `ROW_NUMBER()` gives each row a unique number.

---

# 16. PARTITION BY

Divides data into groups before applying a window function.

```sql
ROW_NUMBER() OVER (
    PARTITION BY city
    ORDER BY amount DESC
)
```

This can be used to find:

* Top 3 orders in each city
* Top customers in each category
* Rankings within groups

---

# 17. LAG()

Returns a value from a previous row.

```sql
SELECT
    customer_name,
    order_date,
    amount,
    LAG(amount) OVER (
        PARTITION BY customer_name
        ORDER BY order_date
    ) AS previous_amount
FROM orders;
```

Useful for comparing:

* Current vs previous order
* Current vs previous month
* Sales changes
* Customer purchasing behavior

---

# 18. Running Total

A running total calculates cumulative values over time.

```sql
SELECT
    order_date,
    amount,
    SUM(amount) OVER (
        ORDER BY order_date
    ) AS running_total
FROM orders;
```

Useful for tracking cumulative revenue or sales.

---

# 19. Percentage Analysis

SQL can be used to calculate each group's contribution to total sales.

```sql
SELECT
    category,
    SUM(amount) AS total_sales,
    ROUND(
        SUM(amount) * 100.0 /
        SUM(SUM(amount)) OVER (),
        2
    ) AS sales_percentage
FROM orders
GROUP BY category;
```

Useful for:

* Revenue contribution
* Market share
* Category analysis
* Customer contribution

---

# 20. Date Analysis

SQL provides functions for working with dates.

Example:

```sql
SELECT
    DATE_FORMAT(order_date, '%Y-%m') AS sales_month,
    SUM(amount) AS total_sales
FROM orders
GROUP BY DATE_FORMAT(order_date, '%Y-%m')
ORDER BY sales_month;
```

Useful for:

* Daily analysis
* Monthly sales
* Yearly analysis
* Trend analysis

---

# 21. Month-over-Month Analysis

Month-over-month analysis compares the current month's performance with the previous month.

Common approach:

1. Aggregate sales by month
2. Use `LAG()`
3. Compare current and previous values

```sql
WITH monthly_sales AS (
    SELECT
        DATE_FORMAT(order_date, '%Y-%m') AS sales_month,
        SUM(amount) AS total_sales
    FROM orders
    GROUP BY DATE_FORMAT(order_date, '%Y-%m')
)
SELECT
    sales_month,
    total_sales,
    LAG(total_sales) OVER (
        ORDER BY sales_month
    ) AS previous_month_sales
FROM monthly_sales;
```

---

# 22. Top-N Analysis

Top-N analysis identifies the highest-performing records.

Examples:

* Top 5 customers
* Top 3 products
* Top 3 orders by city
* Top 2 customers per category

Common SQL techniques:

* `ORDER BY`
* `LIMIT`
* `RANK()`
* `ROW_NUMBER()`
* `PARTITION BY`

---

# 23. Customer Analysis

SQL can be used to understand customer behavior.

Examples:

* Total spending per customer
* Average order value
* Number of orders
* Highest order
* Previous order
* Customer ranking
* Customer contribution to revenue

---

# 24. Product Analysis

Product analysis can identify:

* Highest-selling products
* Most popular products by quantity
* Product revenue
* Product rankings
* Products above average revenue

---

# 25. Sales Analysis

SQL can be used to analyze:

* Total revenue
* Sales by city
* Sales by category
* Monthly sales
* Running sales
* Sales percentages
* Month-over-month changes

---

# 26. Business-Oriented SQL

The main goal of SQL analysis is not only to write queries, but to answer business questions.

Examples:

* Which customers generate the most revenue?
* Which category performs best?
* Which city has the highest sales?
* Which products are most popular?
* How are sales changing over time?
* Which customers have above-average spending?

---

# 🛠️ Tools

* MySQL
* MySQL Workbench
* SQL
* GitHub

# 🎯 Learning Outcome

Through theory and practical exercises, I am developing the ability to:

* Write structured SQL queries
* Analyze raw datasets
* Solve business problems
* Work with aggregated data
* Use subqueries and CTEs
* Apply window functions
* Perform customer and product analysis
* Analyze sales trends
* Translate business questions into SQL queries

## 📌 Related Practical Project

I also created a separate SQL practice project containing **30 practical questions** using a 100-row e-commerce dataset.

**Skills:** SQL | MySQL | Data Analysis | CTEs | Subqueries | Window Functions | Business Analysis
