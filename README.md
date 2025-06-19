# 📊 **Coffee_Shop_Sales_Analysis - SQL Data Analysis Project**
> A PostgreSQL project analyzing sales data from a fictional coffee shop chain to uncover key business insights.
---
## Tool Used
- **PostgreSQL**
- CSV Data (imported loccaly)
---
## **Project Type**
- Portfolio / Self-Initiated
---
## **📌 Project Overview**
This SQL-based project investigate coffee shop sales and operations data to uncover business insights, improve decision-making, and enhance store performance. The dataset was imported into a PostgreSQL database from CSV format and explored using SQL queries only.

---
## 🎯 Objectives
- **Identify top-selling products** by category
- **Compare total revenue** across store locations for January 2023
- **Analyze sales patterns** before 12:00 PM to optimize staffing
- **Uncover pricing anomalies** and high-value products
- **Explore customer behavior** based on peak vs. off-peak hours
- **Detect outliers** in daily sales activity
---
## 🗃️ Project Files
| File | Description |
|------|-------------|
| `coffee_shop_analysis.sql` | SQL queries for all business questions |
| `sample_data.csv` | A 11-row sample from the original dataset |
| `README.md` | Project overview, objectives, and instructions |
| `LICENSE` | MIT license for open-source use |
---
## 🔍 Business Questions Answered
| # | Question |
|--:|----------|
| 1 | What are the **Top 5 Most Frequently Sold Products** by Product Category? |
| 2 | How much **Revenue** did each store generate in **January 2023**? |
| 3 | What are the **Unique Products** sold in the **Lower Manhattan** store? |
| 4 | What is the **Total Number of Transactions** before 12:00 PM? |
| 5 | What’s the **Average Revenue per Transaction** during **Peak vs. Non-Peak** hours? |
| 6 | Which **Product Has the Most Price Fluctuations**? |
| 7 | Which **Products Were Sold in Every Store** at least once? |
| 8 | What are the **Top 5 Days with the Highest Deviation** from average daily quantity sold? |
| 9 | Which **Stores Had Avg Unit Prices > $2.50**?
| 10 | Which **Product Has the Highest Avg Sales per Transaction** in each store? |
---
## Top 5 Most Sold Products by category
WITH Ranked_Sales AS (
Select
		product_id,
		product_type,
		product_category,
		SUM(transaction_quantity) AS Total_Quantity_Sold,
		TO_CHAR(SUM(transaction_quantity * unit_price), 'L999,999,999') AS Total_Sales,
Rank() OVER (PARTITION BY product_category ORDER BY SUM(transaction_quantity) DESC) Sales_Rank
from Coffee_Shop
Group By product_id, product_type, product_category
)

Select * From Ranked_Sales
Where Sales_Rank <=5;
|![Top 5 Most Frequently Sold Product](https://github.com/user-attachments/assets/538dcc93-ea90-4601-b5e0-be97b7d2cace)

## Total Revenue For Each Store in January
Select
	  store_id,
	  store_location,
	  SUM(transaction_quantity) AS Total_Quantity_SoldPerStore,
	  TO_CHAR(SUM(transaction_quantity * unit_price), 'L999,999,999') AS Total_SalesPerSotre
FROM Coffee_Shop
WHERE transaction_date BETWEEN '2023-01-01' and '2023-01-30'
Group By store_id, store_location
ORDER BY TO_CHAR(SUM(transaction_quantity * unit_price), 'L999,999,999') DESC;
![Rev Per Store for Jan](https://github.com/user-attachments/assets/d6155395-27ab-45d4-bfa4-8c2f30f54a91)

## Top 5 Days with Deviation
Select 
	  To_Char(a.transaction_date, 'MM-DD') AS Day,
	  a.Total_Qty,
	  ROUND(b.Avg_Qty, 0) AS Avg_Qty,
	  ROUND(ABS(a.Total_Qty::Numeric - b.Avg_Qty), 0) AS Deviation
FROM (SELECT 
	   transaction_date,
	   SUM(transaction_quantity) AS Total_Qty
	   FROM Coffee_Shop
	   GROUP BY transaction_date) a
CROSS JOIN (SELECT AVG(Total_Qty) AS Avg_Qty
		FROM (SELECT 
		SUM(transaction_quantity) AS Total_Qty
        FROM Coffee_Shop
        GROUP BY transaction_date)) b
ORDER BY Deviation DESC
Limit 5;
![Top 5 Days With Deviation ](https://github.com/user-attachments/assets/864cb165-4939-466b-afcb-3c38ed9db50e)
