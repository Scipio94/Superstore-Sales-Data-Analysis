~~~ SQL
-- 1. What was the most profitable region, state, and city
/*Region sale totals*/
SELECT
  DISTINCT Region,
  ROUND(SUM(Sales),2) AS Region_Sale_Total
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`
GROUP BY Region 
ORDER BY Region_Sale_Total DESC;
-- Western region was the most profitable with a sales total of $710,219.68

/*Western region top state*/
SELECT 
  DISTINCT State, 
  ROUND(SUM(Sales) OVER (PARTITION BY State),2) AS West_Reg_State_Sales,
  ROUND(SUM(Sales) OVER (),2) AS West_Region_Total, 
  ROUND((SUM(Sales) OVER (PARTITION BY State))/(SUM(Sales) OVER ()),2) AS West_Region_State_Pct
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`
WHERE Region = 'West'
ORDER BY West_Reg_State_Sales DESC;
-- California was the most profitable state in the western region with a sales total of $446,306.46, accunting for 63% of the Western region's total sales

/*State Sale Totals*/
SELECT
  State,
  ROUND(SUM(Sales),2) AS State_Sale_Total
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`
GROUP BY State
ORDER BY State_Sale_Total DESC;
-- California was the most profitable state with a sales total of $446,306.46

/*California Cities*/
SELECT 
  DISTINCT City,
  State,
  ROUND(SUM(Sales) OVER (PARTITION BY City,State),2) AS Cali_City_Sales_Totals,
  ROUND(SUM(Sales) OVER (),2) AS Cali_State_Sales_Total,
  ROUND((SUM(Sales) OVER (PARTITION BY City,State))/(SUM(Sales) OVER ()),2) AS Cali_City_Revenue_Pct
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`
WHERE state = 'California'
ORDER BY Cali_City_Sales_Totals DESC;
-- Los Angeles was the most profitable city in California with a sales total of $173,420.18, accounting for 39% of the sales total for the state of California

/*City Sale Total*/
SELECT
  City,
  State,
  ROUND(SUM(Sales),2) AS City_Sale_Total
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`
GROUP BY City,State
ORDER BY City_Sale_Total DESC;
-- New York City was the most profitable city in the dataset with a sales total of $252,462.55

-- 2. What was the most profitable category and sub category?

/*Category*/
SELECT 
  DISTINCT Category,
  ROUND(SUM(Sales) OVER (PARTITION BY Category),2) AS Cat_Sales_Total,
  ROUND(SUM(Sales) OVER (),2) AS Total_Sales,
  ROUND((SUM(Sales) OVER (PARTITION BY Category))/(SUM(Sales) OVER ()),2) AS Category_Pct
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`
ORDER BY Cat_Sales_Total DESC;
-- Technology was the most profitable category with a sales total of $827,455.87, accounting for 37% of the total sales in the dataset

/*Most profitable product in Technology Category*/
SELECT
  DISTINCT product_name,
  ROUND(SUM(Sales) OVER (PARTITION BY Product_Name),2) AS Tech_Product_Sales_Total,
  ROUND(SUM(Sales) OVER (),2) AS Tech_Category_Sales_Total,
  ROUND((SUM(Sales) OVER (PARTITION BY Product_Name))/(SUM(Sales) OVER ()),2) AS Tech_Product_Sales_Pct
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`
WHERE Category = 'Technology'
ORDER BY Tech_Product_Sales_Total DESC;
--The Canon imageCLASS 2200 Advanced Copier was the most profitable product in the Techonology category, totaling $61,599.82 in sales, accounting for 7% of totals sales in the technology category

/*Sub-Category*/
SELECT
  DISTINCT Sub_Category,
  ROUND(SUM(Sales) OVER (PARTITION BY Sub_Category),2) AS Sub_Category_Sales_Total
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`
ORDER BY Sub_Category_Sales_Total DESC;
-- The Sub-Catgory phones had the highest total of sales, $327,782.45.

/*Product Sub-Category Phones*/
SELECT 
  DISTINCT Product_Name,
  ROUND(SUM(Sales) OVER (PARTITION BY Product_Name),2) AS Product_Sales_Totals,
  ROUND(SUM(Sales) OVER (),2) AS Phones_Sales_Total,
  ROUND((SUM(Sales) OVER (PARTITION BY Product_Name))/(SUM(Sales) OVER ()),3) AS Phone_Sub_Category_Product_Pct
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`
WHERE Sub_Category = 'Phones'
ORDER BY Product_Sales_Totals DESC;
-- The Samsung Galaxy Mega 6.3 accounted for the most totals sales in the sub-category phones, $13943.67, 4.3% of total sales of the Phone sub-category

-- 3. What is the most profitable product?
/*Product Metrics*/
SELECT 
  DISTINCT Product_Name,
  ROUND(SUM(Sales) OVER (PARTITION BY Product_Name),2) AS Product_Sales_Total,
  ROUND(SUM(Sales) OVER (),2) AS Total_Sales,
  ROUND((SUM(Sales) OVER (PARTITION BY Product_Name))/(SUM(Sales) OVER ()),2) AS Product_Sales_Pct 
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`
ORDER BY Product_Sales_Total DESC;
-- Canon imageCLASS 2200 Advanced Copier was the product with the most in total sales, $61599.82, which accounted for 3% of the total sales.


-- 4. Shipping
SELECT 
  DISTINCT Ship_Mode,
  COUNT(*) OVER (PARTITION BY Ship_Mode) AS Shipping_Count,
  COUNT(*) OVER () AS Total_Orders,
  ROUND((COUNT(*) OVER (PARTITION BY Ship_Mode))/(COUNT(*) OVER ()),2) AS Shipping_Mode_Pct
  -- ROUND(SUM(sales) OVER (PARTITION BY Ship_Mode),2) AS Ship_Mode_Total_Sales,
  -- ROUND(AVG(sales) OVER (PARTITION BY Ship_Mode),2) AS Ship_Mode_Price_Avg,
  -- PERCENTILE_DISC(Sales,0.5) OVER (PARTITION BY Ship_Mode) AS Ship_Mode_Price_Median
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`
ORDER BY Shipping_Count DESC;
-- Majority of orders were shipped standard class, 5,859, 60%

/*Correlation between days difference between Ship Date and Order Date*/
SELECT 
  CORR(Days_Difference, Sales) AS Corr_Coefficient
FROM
(SELECT 
  Ship_Mode,
  Ship_Date,
  Order_Date,
  EXTRACT( DAY FROM (Ship_Date - Order_Date)) AS Days_Difference,
  Sales
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`);
-- There is no correlation between the amount of time between order date and shipping date. Indicated in the correlation coeffiienct value -0.01 

-- 5. What was the most profitable year
/*Year Sales Total*/
SELECT 
  DISTINCT sub.Year,
  ROUND(SUM(sub.Sales) OVER (PARTITION BY sub.Year),2) AS Year_Sales_Total,
  ROUND(SUM(sub.sales) OVER (),2) AS Total_Sales,
  ROUND((SUM(sub.Sales) OVER (PARTITION BY sub.Year))/( SUM(sub.sales) OVER ()),2) AS Sales_Pct
FROM
(SELECT
  EXTRACT(YEAR FROM Order_Date) AS Year,
  Sales
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`) AS sub
ORDER BY Year_Sales_Total DESC;
-- 2018 was the recorded the most sales,722052.02, accounting for 32% of total sales 

/*YoY Trends*/
CREATE TEMP TABLE t1 AS
SELECT
  sub1.Year, 
  sub1.Year_Sales_Total,
  COALESCE(ROUND(sub1.Year_Sales_Total - LAG(sub1.Year_sales_total,1) OVER (ORDER BY sub1.Year),2),0) AS YoY_Total_Sales_Change,
  COALESCE(ROUND((sub1.Year_Sales_Total - LAG(sub1.Year_sales_total,1) OVER (ORDER BY sub1.Year))/(LAG(sub1.Year_Sales_Total,1) OVER (ORDER BY sub1.Year)),2),0) AS YoY_Total_Sales_Pct_Change
FROM
(SELECT 
  DISTINCT sub.Year,
  ROUND(SUM(sub.Sales) OVER (PARTITION BY sub.Year),2) AS Year_Sales_Total,
  ROUND(SUM(sub.sales) OVER (),2) AS Total_Sales,
  ROUND((SUM(sub.Sales) OVER (PARTITION BY sub.Year))/( SUM(sub.sales) OVER ()),2) AS Sales_Pct
FROM
(SELECT
  EXTRACT(YEAR FROM Order_Date) AS Year,
  Sales
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`) AS sub
ORDER BY Year_Sales_Total DESC) AS sub1
ORDER BY sub1.Year;
-- Upward trend in Year Sales total as well as YoY change with an average YoY sales increase of 60548.95, a 12% YoY increase
SELECT
  ROUND(AVG(t1.Yoy_Total_Sales_Change),2) AS Avg_YoY_Change,
  ROUND(AVG(t1.YoY_Total_Sales_Pct_Change),2) AS Avg_YoY_Pct_Change
FROM t1;

SELECT *
FROM t1
~~~
