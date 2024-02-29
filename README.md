# Sales-Performance-Dashboard using SQL and Power BI
This project aimed to develop a comprehensive Sales Performance Dashboard to provide actionable insights into sales data, leveraging SQL for data exploration and cleaning, and Power BI for dashboard building. The final dashboard comprises various visualizations highlighting crucial aspects of sales performance, along with slicers for enhanced interactivity.

## Steps Involved:
### 1. Data Cleansing & Transformation (SQL)
To create the necessary data model for doing analysis and fulfilling the business needs defined in the user stories the following tables were extracted using SQL.

One data source (sales budgets) was provided in Excel format and was connected to the data model in a later step of the process.

Below are the SQL statements for cleansing and transforming necessary data.

**DIM_Calendar:**

```
SELECT [DateKey]
      ,[FullDateAlternateKey] AS Date
      --,[DayNumberOfWeek]
      ,[EnglishDayNameOfWeek] AS Day
      --,[SpanishDayNameOfWeek]
      --,[FrenchDayNameOfWeek]
      --,[DayNumberOfMonth]
      --,[DayNumberOfYear]
      ,[WeekNumberOfYear] AS WeekNr
      ,[EnglishMonthName] AS Month,
      LEFT([EnglishMonthName],3) AS MonthShort
      --,[FrenchMonthName]
      ,[MonthNumberOfYear] AS MonthNo
      ,[CalendarQuarter] AS Quarter
      ,[CalendarYear] AS Year
      --,[CalendarSemester]
      --,[FiscalQuarter]
      --,[FiscalYear]
      --,[FiscalSemester]
  FROM [AdventureWorksDW2022].[dbo].[DimDate]
  WHERE 
   CalendarYear >= 2019
```


**DIM_Customers:**

```
-- Cleansed DIM_Customer Table
SELECT c.customerkey AS CustomerKey,
      --,[GeographyKey]
      --,[CustomerAlternateKey]
      --,[Title]
      c.firstname AS [First Name],
      --,[MiddleName]
     c.lastname AS [Last Name],
	 c.firstname + ' ' + lastname AS [Full Name],
      --,[NameStyle]
      --,[BirthDate]
      --,[MaritalStatus]
      --,[Suffix]
	  CASE c.gender WHEN 'M' THEN 'Male' WHEN 'F' THEN 'Female' END AS Gender,
      --,[Phone]
      c.datefirstpurchase AS DateFirstPurchase,
      --,[CommuteDistance]
	  g.city AS [Customer City] -- Joined in Customer City from Geography Table
  FROM 
  dbo.dimcustomer AS c
  LEFT JOIN dbo.dimgeography AS g ON g.geographykey = c.geographykey
  ORDER BY
  CustomerKey ASC -- Ordered List by CustomerKey 

```

**DIM_Products:**

```
 --Cleansed DIM_Product Table
 SELECT p.[ProductKey],
      p.[ProductAlternateKey] AS ProductItemCode,
      --,[ProductSubcategoryKey]
      --,[WeightUnitMeasureCode]
      --,[SizeUnitMeasureCode]
      p.[EnglishProductName] AS [Product Name],
      ps.EnglishProductSubCategoryName AS [Sub Category], -- Joined in from Category Table
	  pc.EnglishProductCategoryName AS [Product Category],  -- Joinded in from Category Table
      --,[FrenchProductName]
      --,[StandardCost]
      --,[FinishedGoodsFlag]
      p.[Color] AS [Product Color],
      --,[SafetyStockLevel]
      --,[ReorderPoint]
      --,[ListPrice]
      p.[Size] AS [Product Size],
      --,[SizeRange]
      --,[Weight]
      --,[DaysToManufacture]
      p.[ProductLine] AS [Product Line],
      --,[DealerPrice]
      --,[Class]
      --,[Style]
      p.[ModelName] AS [Product Model Name],
	  --,[LargePhoto]
      p.[EnglishDescription] AS [Product Description],
      p.[Status] AS Example,
	  ISNULL (p.status, 'Outdated') AS [Product Status]
  FROM 
  [dbo].[DimProduct] as p
  LEFT JOIN dbo.DimProductSubcategory AS ps ON ps.ProductSubcategorykey = p.ProductSubcategoryKey
  LEFT JOIN dbo.DimProductCategory AS pc ON ps.ProductCategoryKey = pc.ProductCategoryKey
  ORDER BY
  p.ProductKey ASC

```

**FACT_InternetSales:**

```
--Cleansed FACT_InternetSales Table

SELECT [ProductKey]
      ,[OrderDateKey]
      ,[DueDateKey]
      ,[ShipDateKey]
      ,[CustomerKey]
      --,[PromotionKey]
      --,[CurrencyKey]
      --,[SalesTerritoryKey]
      ,[SalesOrderNumber]
      ,[SalesAmount]
      --,[TaxAmt]
      --,[Freight]
      --,[CarrierTrackingNumber]
      --,[CustomerPONumber]
      --,[OrderDate]
      --,[DueDate]
      --,[ShipDate]
  FROM [dbo].[FactInternetSales]
  WHERE 
  LEFT (OrderDateKey, 4) >= YEAR(GETDATE()) -2  -- Ensures we always only bring two years of date from extraction.
  ORDER BY
  OrderDate ASC
```

### 2. Data Modelling
Below is a screenshot of the data model after cleansed and prepared tables were read into Power BI.

This data model also shows how FACT_Budget has been connected to FACT_InternetSales and other necessary DIM tables.

![5](https://github.com/micky-26/Sales-Performance-Dashboard/assets/106061980/6e51b990-939c-41a5-b65d-4d7e9b109864)

### 3. Sales Performance Dashboard Building using Power BI


The finished sales management dashboard with one page works as a dashboard and overview, with two other pages focused on combining tables for necessary details and visualizations to show sales over time, per customer, and per product.
