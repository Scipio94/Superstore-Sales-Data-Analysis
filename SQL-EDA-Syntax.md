~~~ SQL 
/*DISTINCT Customer Names*/
SELECT 
  COUNT(DISTINCT Customer_Name) AS Distinct_Customer_Count
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;
-- 793 distinct customers

/*Date Min and Max*/
SELECT 
  MIN(Order_Date) AS Min_Date,
  Max(Order_Date) AS Max_Date
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;
-- From Jan 3, 2015 to Dec 30, 2018

/*Dataset Range*/
SELECT 
   EXTRACT( DAY FROM (Max(Order_Date) - MIN(Order_Date))) AS Total_Days
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;
-- 1,457 DAYS
-- Nearly 4 years

/*Sales Range*/
SELECT
  MIN(Sales) AS Min_Sale,
  Max(Sales) AS Max_Sales
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;
-- Min $0.44, $Max 22,638.48

/*Sales Distribution*/
SELECT 
  DISTINCT PERCENTILE_DISC(sales, 0.50) OVER () AS Median,
  ROUND(AVG(Sales) OVER (),2) AS Mean
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;
-- Median $54.48, Mean $230.77
-- Indicative of a skew

/*Distinct State Count*/
SELECT 
  COUNT(DISTINCT State) AS Distinct_State_Count
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;
-- 49 states

/*Segments*/
SELECT
  DISTINCT Segment
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;

-- 3 segments - Consumer, Corporate, Home Office

/*Product Name*/
SELECT 
  DISTINCT Product_Name
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;
-- 1849 products

/*Category*/
SELECT
  DISTINCT Category
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;
-- 3 categories

/*Sub Category*/
SELECT
  DISTINCT sub_Category
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;
--17 categories

/*Region*/
SELECT
  DISTINCT Region
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;
-- 4 regions - Central, East, West, South

/*Cities*/
SELECT 
  COUNT (DISTINCT city) AS City_Count
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;
-- 529 Cities

/*Ship mode*/
SELECT
  DISTINCT Ship_Mode
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;
-- 4 ship modes

/*Sale distribution*/
SELECT
  DISTINCT TRUNC(Sales,-2) AS Sale_Bucket,
  COUNT(*) AS Bucket_Count
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`
GROUP BY Sale_Bucket
ORDER BY Sale_Bucket;
-- Majority of the sale prices in the dataset, 6101, are between $0 and $100
~~~
