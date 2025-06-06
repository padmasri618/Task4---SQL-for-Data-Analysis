## Objective
--The objective of this task is to demonstrate SQL skills by performing data analysis on an eCommerce dataset. This includes writing optimized SQL queries using joins, aggregate functions, views, and indexes. The goal is to derive meaningful insights such as sales trends, customer revenue, and country-wise performance.
## Code used
--create schema ecommerce;
CREATE TABLE ecommerce_orders (
    InvoiceNo VARCHAR(20),
    StockCode VARCHAR(20),
    Description TEXT,
    Quantity INT,
    InvoiceDate DATETIME,
    UnitPrice DECIMAL(10,2),
    CustomerID VARCHAR(20),
    Country VARCHAR(50)
);

INSERT INTO ecommerce_orders 
(InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
VALUES 
('536365', '85123A', 'WHITE HANGING HEART T-LIGHT HOLDER', 6, '2010-12-01 08:26:00', 2.55, '17850', 'United Kingdom'),
('536365', '71053', 'WHITE METAL LANTERN', 6, '2010-12-01 08:26:00', 3.39, '17850', 'United Kingdom'),
('536366', '84406B', 'CREAM CUPID HEARTS COAT HANGER', 8, '2010-12-01 08:28:00', 2.75, '17850', 'United Kingdom');

# Total Sales by Country
SELECT Country, SUM(Quantity * UnitPrice) AS TotalSales
FROM ecommerce_orders
GROUP BY Country
ORDER BY TotalSales DESC;

#Average Order Value per Customer
SELECT CustomerID, AVG(Quantity * UnitPrice) AS AvgOrderValue
FROM ecommerce_orders
GROUP BY CustomerID
ORDER BY AvgOrderValue DESC;

#Top 5 Best-Selling Products
SELECT Description, SUM(Quantity) AS TotalSold
FROM ecommerce_orders
GROUP BY Description
ORDER BY TotalSold DESC
LIMIT 5;

#Number of Orders per Day
SELECT DATE(InvoiceDate) AS OrderDate, COUNT(DISTINCT InvoiceNo) AS NumOrders
FROM ecommerce_orders
GROUP BY OrderDate
ORDER BY OrderDate;

#Using Subquery: Highest Spending Customer
SELECT CustomerID, SUM(Quantity * UnitPrice) AS TotalSpent
FROM ecommerce_orders
GROUP BY CustomerID
HAVING TotalSpent = (
    SELECT MAX(total)
    FROM (
        SELECT CustomerID, SUM(Quantity * UnitPrice) AS total
        FROM ecommerce_orders
        GROUP BY CustomerID
    ) AS sub
);

#Total Sales by Country (Using JOIN + SUM)
SELECT Country, SUM(Quantity * UnitPrice) AS TotalSales
FROM ecommerce_orders 
INNER JOIN customers  ON CustomerID = CustomerID
GROUP BY Country
ORDER BY TotalSales DESC;

#Create a View for Total Sales
CREATE VIEW country_sales_summary AS
SELECT Country, SUM(Quantity * UnitPrice) AS TotalSales
FROM ecommerce_orders
GROUP BY Country;
SELECT * FROM country_sales_summary
ORDER BY TotalSales DESC;

#Create an Index to Optimize Sales Query
CREATE INDEX idx_customer_sales ON ecommerce_orders (CustomerID, InvoiceDate);

#Total Revenue by Customer (Aggregate + View + JOIN)
CREATE VIEW customer_revenue_view AS
SELECT CustomerID, SUM(Quantity * UnitPrice) AS TotalRevenue, Country
FROM ecommerce_orders e
INNER JOIN customers  ON CustomerID = CustomerID
GROUP BY CustomerID, Country;
SELECT * FROM customer_revenue_view
ORDER BY TotalRevenue DESC;

---
## Output Screenshot
![Screenshot 2025-06-06 160115](https://github.com/user-attachments/assets/47ca5f3f-467d-4d22-a979-8233717708c9)
![Screenshot 2025-06-06 155842](https://github.com/user-attachments/assets/bd1818d5-4400-466d-b7cd-e2654167b95c)
![Screenshot 2025-06-06 155825](https://github.com/user-attachments/assets/6101a240-6f10-4da7-bdd7-63382766e84d)
![Screenshot 2025-06-06 155715](https://github.com/user-attachments/assets/20dbcb1c-c45b-44a9-be23-8e67e2fe09c9)
![Screenshot 2025-06-06 155701](https://github.com/user-attachments/assets/169c9ac5-9618-46c7-b96c-ba56bc088548)
![Screenshot 2025-06-06 155546](https://github.com/user-attachments/assets/2c33c203-0f80-49e2-b213-08bef0c55f73)
--
## Dataset Explanation
-- The dataset used is an online eCommerce transactional dataset. It contains the following columns:

InvoiceNo: Unique invoice number for each order

StockCode: Unique product code

Description: Name/description of the product

Quantity: Number of units purchased

InvoiceDate: Date and time of the invoice

UnitPrice: Price per unit of the product

CustomerID: Unique ID for each customer

Country: Customer's country of residence

---
## Insights, Patterns, Anomalies
-- United Kingdom contributes the highest total sales, followed by other European countries.

Some countries like Netherlands, Germany, and France have high-value purchases with fewer customers.

A few transactions have negative quantities, which indicate returns or cancellations.

The average revenue per customer varies significantly; a few customers contribute disproportionately to total revenue.

The views created (country_sales_summary, customer_revenue_view) help simplify frequent reporting.

---
## Conclusion
Using SQL, we were able to derive meaningful business insights from a structured eCommerce dataset. This task demonstrated the use of various SQL techniques such as aggregate functions (SUM), JOINs, views, and indexes to optimize queries. The analysis provides a foundation for further business intelligence reporting and dashboarding.

