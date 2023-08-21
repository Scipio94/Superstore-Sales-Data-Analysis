~~~ SQL
-- 1 What was the average rate of change in quarterly sales ?
/*Creating TEMP TABLE to Calculate Quarterly Change*/
CREATE TEMP TABLE f1 AS 
SELECT 
  EXTRACT(QUARTER FROM Order_Date) AS Qtr,
  EXTRACT (MONTH FROM Order_Date) AS Month,
  EXTRACT(YEAR FROM Order_Date) AS Year,
  Sales
FROM `single-being-353600.Superstore_Sales_Data.Sales_Data`;

/*Calculating Average Rate of Quartely Change*/
SELECT
  ROUND(AVG(sub1.Qtr_Change),2) AS Avg_Qtr_Change,
  ROUND(AVG(sub1.Qtr_Change_Pct),2) AS Avg_Qtr_Change_Pct
FROM
(SELECT
  sub.Year,
  sub.Qtr,
  sub.Qtr_Sales,
  ROUND(COALESCE(sub.Qtr_Sales - LAG(sub.Qtr_Sales,1) OVER (ORDER BY sub.Year,sub.Qtr),0),2) AS Qtr_Change,
  ROUND(COALESCE((sub.Qtr_Sales - LAG(sub.Qtr_Sales,1) OVER (ORDER BY sub.Year,sub.Qtr))/(LAG(sub.Qtr_Sales,1) OVER (ORDER BY sub.Year,sub.Qtr)),0),2) AS Qtr_Change_Pct
FROM 
(SELECT 
  DISTINCT f1.Year,
  f1.Qtr,
  ROUND(SUM(f1.Sales) OVER (PARTITION BY f1.Year,f1.Qtr),2) AS Qtr_Sales,
FROM f1
ORDER BY f1.Year,f1.Qtr) AS sub
ORDER BY sub.Year,sub.Qtr) AS sub1;
-- On average there was a sales increase of $12,780.33, 18%, per quarter

/*Most Profitable Quarter*/
SELECT 
  DISTINCT f1.Year,
  f1.Qtr,
  ROUND(SUM(f1.Sales) OVER (PARTITION BY f1.Year,f1.Qtr),2) AS Qtr_Sales,
FROM f1
ORDER BY f1.Year,f1.Qtr
-- Qtr 4 of 2018 was the most profitable qtr in the dataset, totaling $278,416.67 in sales.
~~~
