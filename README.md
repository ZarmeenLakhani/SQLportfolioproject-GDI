# FinTech Market Analysis Project

## Project Overview
This project focuses on using SQL to analyze a **Revenue Operations (RevOps)** dataset. The goal is to simulate real-world challenges faced by sales, marketing, and operations teams across different industries. By querying and extracting insights from the data, we aim to understand sales performance, marketing campaign effectiveness, customer churn, and more.

The dataset includes multiple related tables:
- **Sales Transactions**
- **Customers**
- **Marketing Campaigns**
- **Leads**
- **Sales Reps**

## Objectives
1. Highlight the KPIs  

## Dataset Dictionary

### Tables

1. **Sales_Transactions**:
   Contains records of sales transactions, including customer and product information.
   - **Fields**: `transaction_id`, `customer_id`, `product_id`, `sales_amount`, `transaction_date`, `sales_rep_id`, `region`

2. **Customers**:
   Stores customer details such as name, segment (SMB or Enterprise), and churn information.
   - **Fields**: `customer_id`, `customer_name`, `customer_segment`, `signup_date`, `churn_date`

3. **Marketing_Campaigns**:
   Includes marketing campaigns, their budgets, start/end dates, and leads generated.
   - **Fields**: `campaign_id`, `campaign_name`, `start_date`, `end_date`, `budget`, `leads_generated`

4. **Leads**:
   Tracks leads generated from various sources and their status (Converted, Lost, etc.).
   - **Fields**: `lead_id`, `lead_source`, `lead_status`, `customer_id`, `campaign_id`, `lead_date`

5. **Sales_Reps**:
   Information about sales representatives and their regions.
   - **Fields**: `sales_rep_id`, `rep_name`, `region`, `total_sales`

## SQL Concepts Used
- **Joins**: Combine data from multiple tables to analyze relationships between sales, leads, and campaigns.
- **Aggregations**: Use of `SUM()`, `COUNT()`, `AVG()` for calculating total sales, leads, and revenue per campaign/region.
- **Data Cleaning**: Handling missing data and cleaning fields using `COALESCE()`, `CASE`, and `NULL` handling.
- **Grouping & Filtering**: Using `GROUP BY`, `HAVING` to group data and filter results by conditions.
- **Indexes**: Understanding the importance of indexing for query optimization (theoretical concept).

## Queries

### 1. **Total Sales by Region**:
```sql
SELECT region, SUM(sales_amount) AS total_sales
FROM Sales_Transactions
GROUP BY region;
```

## 2. Sales Conversion Rate by Marketing Campaign:
```sql
SELECT mc.campaign_name, 
       COUNT(l.lead_id) AS total_leads,
       SUM(CASE WHEN l.lead_status = 'Converted' THEN 1 ELSE 0 END) AS converted_leads,
       ROUND(SUM(CASE WHEN l.lead_status = 'Converted' THEN 1 ELSE 0 END) / CAST(COUNT(l.lead_id) AS DECIMAL) * 100, 2) AS conversion_rate
FROM Marketing_Campaigns mc
JOIN Leads l ON mc.campaign_id = l.campaign_id
GROUP BY mc.campaign_name;
```

## 3. Customer Lifetime Value (CLV):
```sql
SELECT c.customer_name, 
       COALESCE(SUM(st.sales_amount), 0) AS total_revenue,
       CASE 
           WHEN c.churn_date IS NULL THEN 'Active'
           ELSE 'Churned'
       END AS customer_status
FROM Customers c
LEFT JOIN Sales_Transactions st ON c.customer_id = st.customer_id
GROUP BY c.customer_name, c.churn_date;
```

## 4. Top Marketing Campaigns by Revenue:
```sql
SELECT mc.campaign_name, SUM(st.sales_amount) AS total_revenue
FROM Marketing_Campaigns mc
JOIN Leads l ON mc.campaign_id = l.campaign_id
JOIN Sales_Transactions st ON l.customer_id = st.customer_id
GROUP BY mc.campaign_name
ORDER BY total_revenue DESC;
```

## 5. Churn Rate by Customer Segment:
```sql
SELECT c.customer_segment, 
       COUNT(c.customer_id) AS total_customers,
       SUM(CASE WHEN c.churn_date IS NOT NULL THEN 1 ELSE 0 END) AS churned_customers,
       ROUND(SUM(CASE WHEN c.churn_date IS NOT NULL THEN 1 ELSE 0 END) / CAST(COUNT(c.customer_id) AS DECIMAL) * 100, 2) AS churn_rate
FROM Customers c
GROUP BY c.customer_segment;
```

## Insights & Recommendations
This analysis can help your RevOps team to:

- Optimize marketing spend by focusing on campaigns with higher conversion rates.
- Improve customer retention by analyzing churn patterns in SMB vs. Enterprise segments.
- Increase sales rep productivity by evaluating performance by region.

## Conclusion
This project demonstrates how SQL can be used to uncover actionable insights in a RevOps context. Feel free to explore further or adapt this project to your specific dataset.

