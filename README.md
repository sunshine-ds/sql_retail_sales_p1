# SQL Retail Sales Analysis Project

## Project Overview

**Project Title**: SQL Retail Sales Analysis
**Level**: Beginner
**Database**: `sql_project_p2`

This project is designed to demonstrate SQL skills and techniques commonly used by data analysts to explore, clean, and analyze retail sales data.

The project involves creating a retail sales database, performing data cleaning and exploratory data analysis (EDA), and solving business-related questions using SQL queries.

This project demonstrates practical SQL concepts including data filtering, aggregation, grouping, window functions, Common Table Expressions (CTEs), ranking, and date and time analysis.

---

## Objectives

1. **Set up a retail sales database**: Create a database and a retail sales table to store transaction data.

2. **Data Cleaning**: Identify and remove records containing missing or null values.

3. **Exploratory Data Analysis (EDA)**: Perform basic analysis to understand the number of sales, customers, and available product categories.

4. **Business Analysis**: Use SQL queries to answer business questions and extract useful insights from the retail sales data.

---

# Project Structure

## 1. Database Setup

The project begins by creating a database named `sql_project_p2`.

A table named `retail_sales` is created to store retail transaction data.

The table contains information about:

* Transaction ID
* Sale Date
* Sale Time
* Customer ID
* Customer Gender
* Customer Age
* Product Category
* Quantity Sold
* Price Per Unit
* Cost of Goods Sold (COGS)
* Total Sales

```sql
DROP TABLE IF EXISTS retail_sales;

CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(15),
    age INT,
    category VARCHAR(15),
    quantiy INT,
    price_per_unit FLOAT,
    cogs FLOAT,
    total_sale FLOAT
);
```

---

## 2. Data Exploration & Cleaning

Before performing the analysis, the dataset was checked for missing or null values.

The following exploratory queries were used:

* Determine the total number of sales.
* Find the total number of unique customers.
* Identify different product categories.
* Check for null values.
* Remove records containing missing values.

### Check for Missing Values

```sql
SELECT *
FROM retail_sales
WHERE 
    transactions_id IS NULL
    OR sale_time IS NULL
    OR gender IS NULL
    OR age IS NULL
    OR category IS NULL
    OR quantiy IS NULL
    OR cogs IS NULL
    OR price_per_unit IS NULL
    OR total_sale IS NULL;
```

### Delete Records with Missing Values

```sql
DELETE
FROM retail_sales
WHERE 
    transactions_id IS NULL
    OR sale_time IS NULL
    OR gender IS NULL
    OR age IS NULL
    OR category IS NULL
    OR quantiy IS NULL
    OR cogs IS NULL
    OR price_per_unit IS NULL
    OR total_sale IS NULL;
```

---

# 3. Data Exploration

### Total Number of Sales

```sql
SELECT COUNT(*) AS total_sales
FROM retail_sales;
```

### Total Number of Unique Customers

```sql
SELECT COUNT(DISTINCT customer_id) AS total_customers
FROM retail_sales;
```

### Identify Product Categories

```sql
SELECT DISTINCT category
FROM retail_sales;
```

---

# 4. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions.

---

### Q1. Retrieve all columns for sales made on '2022-11-05'

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

---

### Q2. Retrieve Clothing Transactions in November 2022

Find all transactions where:

* The category is `Clothing`.
* The quantity sold is greater than 3.
* The transaction was made during November 2022.

```sql
SELECT *
FROM retail_sales
WHERE 
    sale_date BETWEEN '2022-11-01' AND '2022-11-30'
    AND category = 'Clothing'
    AND quantiy > 3;
```

---

### Q3. Calculate Total Sales for Each Category

This query calculates the total revenue and total number of orders for each product category.

```sql
SELECT 
    category,
    SUM(total_sale) AS net_sales,
    COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
```

---

### Q4. Find the Average Age of Customers Who Purchased Beauty Products

```sql
SELECT 
    ROUND(AVG(age), 2) AS avg_age
FROM retail_sales 
WHERE category = 'Beauty';
```

---

### Q5. Find All High-Value Transactions

Retrieve all transactions where the total sale amount is greater than 1000.

```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

---

### Q6. Find Total Transactions by Gender and Category

This query calculates the number of transactions made by each gender in each product category.

```sql
SELECT 
    category,
    gender,
    COUNT(*) AS total_trans
FROM retail_sales
GROUP BY gender, category
ORDER BY category;
```

---

### Q7. Find the Best-Selling Month in Each Year

This query calculates the average monthly sales and identifies the best-selling month in each year.

The query uses a `RANK()` window function with `PARTITION BY`.

```sql
SELECT *
FROM
(
    SELECT 
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER(
            PARTITION BY EXTRACT(YEAR FROM sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales
    GROUP BY 1, 2
) AS t1
WHERE rank = 1;
```

---

### Q8. Find the Top 5 Customers Based on Highest Total Sales

```sql
SELECT 
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

---

### Q9. Find the Number of Unique Customers in Each Category

```sql
SELECT 
    category,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;
```

---

### Q10. Analyze Orders Based on Time Shifts

The day is divided into three shifts:

* **Morning**: Before 12 PM
* **Afternoon**: Between 12 PM and 5 PM
* **Evening**: After 5 PM

A Common Table Expression (CTE) and `CASE` statement are used to categorize each transaction.

```sql
WITH hourly_sale AS
(
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)

SELECT
    shift,
    COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;
```

---

# SQL Concepts Used

This project demonstrates the following SQL concepts:

* `SELECT`
* `WHERE`
* `DISTINCT`
* `COUNT()`
* `SUM()`
* `AVG()`
* `GROUP BY`
* `ORDER BY`
* `LIMIT`
* `BETWEEN`
* `CASE WHEN`
* `EXTRACT()`
* Common Table Expressions (CTEs)
* Window Functions
* `RANK()`
* `PARTITION BY`
* Data Cleaning
* Handling `NULL` values

---

# Findings

The analysis helps provide insights into the retail sales dataset, including:

* Total number of transactions and customers.
* Available product categories.
* Category-wise sales performance.
* High-value transactions.
* Customer purchasing patterns based on gender and product category.
* Average customer age for Beauty category purchases.
* Top 5 customers based on total spending.
* Number of unique customers in each product category.
* Best-selling month for each year.
* Distribution of orders across Morning, Afternoon, and Evening shifts.

---

# Reports

The analysis can be used to generate the following reports:

### Sales Summary

Provides information about total sales, total transactions, and category-wise performance.

### Customer Analysis

Identifies unique customers, top-spending customers, customer age patterns, and gender-based purchasing behavior.

### Category Analysis

Analyzes sales performance and customer distribution across different product categories.

### Time-Based Analysis

Analyzes monthly sales trends and customer purchasing behavior across different shifts during the day.

---

# Conclusion

This project demonstrates an end-to-end SQL data analysis workflow using retail sales data.

The project covers database setup, data cleaning, exploratory data analysis, and business-focused SQL queries. It also demonstrates important SQL concepts such as aggregation, grouping, window functions, ranking, CTEs, and conditional logic.

Through this project, meaningful insights can be extracted about customer behavior, sales performance, product categories, high-value transactions, and time-based sales patterns.

This project is part of my data analytics portfolio and demonstrates practical SQL skills required for entry-level Data Analyst roles.

---

# Tools Used

* PostgreSQL
* pgAdmin 4
* SQL

---

# How to Use

1. **Clone the Repository**: Clone this repository from GitHub.

2. **Create the Database**: Create a PostgreSQL database named `sql_project_p2`.

3. **Create the Table**: Run the table creation query provided in the SQL script.

4. **Import the Dataset**: Load the retail sales dataset into the `retail_sales` table.

5. **Run the Queries**: Execute the SQL queries to perform data cleaning, exploration, and business analysis.

6. **Modify and Explore**: Modify the existing queries or create additional queries to explore more insights from the dataset.

---

## Author

**Divyanshi Kumari**

This project is part of my data analytics portfolio and showcases my SQL skills in database creation, data cleaning, exploratory data analysis, and business analysis.
