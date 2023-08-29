## Introduction
The purpose of this exercise is to conduct and document an extensive EDA on a mock E-commerce dataset, pointing out aspects such as data validity, consistency, accuracy, as well as several exercise questions done in SQL.

This exercise is done in **MySQL** using the **MySQL Workbench** IDE.
This exercise will be done using [this Dataset](https://docs.google.com/spreadsheets/d/17NYNRCWHbRI4mtDxCwamudDWDpzQuWk_1x0vsCINYZc/edit#gid=1921694854).
You may find the following sections below:
1. Database Connection
2. Exploratory Analysis with SQL
3. SQL Questions

*Note - [Dillinger](https://dillinger.io/) is used to create this Readme file in Markdown format*

## Database Connection
###### Creating Database Schema 
The query below is used to **create** and **use** the new schema called **assignment**.
```sql
CREATE DATABASE assignment;
USE assignment;
```

###### Importing Data from .CSV files as Tables using 'Table Data Import Wizard' in MySQL Workbench
1. To import data from the Data Analyst Assignment [spreadsheet](https://docs.google.com/spreadsheets/d/17NYNRCWHbRI4mtDxCwamudDWDpzQuWk_1x0vsCINYZc/edit#gid=1921694854), first download each sheet as a separate .CSV file.
2. In the MySQL Workbench GUI, Right click on *Tables*, and click on **Table Data Import Wizard** to open a window. Select the .CSV file which you would like to import to a table. Click *"Next"* to continue.
![Image](https://i.imgur.com/lF759x5.png)
3. Choose the schema which you want to create the new table in, and enter the table name. Click *"Next"* to continue.
![Image](https://i.imgur.com/rd2V2ST.png)
4. Ensure all the columns that you want to include in the table are checked, and adjust each column's field types accordingly. Click *"Next"* to execute the import.
![Image](https://i.imgur.com/SnLDgdB.png)
5. The result is, the table is created accordingly within the schema.
![Image](https://i.imgur.com/vjSQ4H5.png)

###### (Alternative) Importing Data from .CSV files as Tables using MySQL Queries
1. There are 4 sheets in the dataset. Create 4 tables in the dataset using the queries below, one table corresponding to one sheet.

###### DimCustomers: 
``` sql
CREATE TABLE DimCustomer (
    CustomerKey INT NOT NULL PRIMARY KEY,
    GeographyKey INT,
    CustomerAlternateKey VARCHAR(255),
    Title VARCHAR(255),
    FirstName VARCHAR(255),
    MiddleName VARCHAR(255),
    LastName VARCHAR(255),
    NameStyle INT,
    BirthDate TEXT,
    MaritalStatus CHAR(1),
    Suffix VARCHAR(255),
    Gender CHAR(1),
    EmailAddress VARCHAR(255),
    YearlyIncome DECIMAL(10,2),
    TotalChildren INT,
    NumberChildrenAtHome INT,
    EnglishEducation VARCHAR(255),
    SpanishEducation VARCHAR(255),
    FrenchEducation VARCHAR(255),
    EnglishOccupation VARCHAR(255),
    SpanishOccupation VARCHAR(255),
    FrenchOccupation VARCHAR(255),
    HouseOwnerFlag INT,
    NumberCarsOwned INT,
    AddressLine1 VARCHAR(255),
    AddressLine2 VARCHAR(255),
    Phone VARCHAR(255),
    DateFirstPurchase TEXT,
    CommuteDistance VARCHAR(255)
);
```

###### DimProduct: 
``` sql
CREATE TABLE DimProduct (
    ProductKey INT NOT NULL PRIMARY KEY,
    ProductAlternateKey VARCHAR(255),
    ProductSubcategoryKey INT,
    WeightUnitMeasureCode VARCHAR(255),
    SizeUnitMeasureCode VARCHAR(255),
    ProductName VARCHAR(255),
    StandardCost DECIMAL(10,4),
    FinishedGoodsFlag INT,
    Color VARCHAR(255),
    SafetyStockLevel INT,
    ReorderPoint INT,
    ListPrice DECIMAL(10,4),
    Size VARCHAR(255),
    SizeRange VARCHAR(255),
    Weight DECIMAL(10,4),
    DaysToManufacture INT,
    ProductLine VARCHAR(255),
    DealerPrice DECIMAL(10,4),
    Class VARCHAR(255),
    Style VARCHAR(255),
    ModelName VARCHAR(255),
    StartDate TEXT,
    EndDate TEXT,
    Status VARCHAR(255)
);
```

###### DimSalesTerritory: 
``` sql
CREATE TABLE DimSalesTerritory (
    SalesTerritoryKey INT NOT NULL PRIMARY KEY,
    SalesTerritoryAlternateKey INT,
    SalesTerritoryRegion VARCHAR(255),
    SalesTerritoryCountry VARCHAR(255),
    SalesTerritoryGroup VARCHAR(255)
);
```

###### FactResellerSales: 
``` sql
CREATE TABLE FactResellerSales (
    ProductKey INT,
    OrderDateKey INT,
    DueDateKey INT,
    ShipDateKey INT,
    ResellerKey INT,
    EmployeeKey INT,
    PromotionKey INT,
    CurrencyKey INT,
    SalesTerritoryKey INT,
    SalesOrderNumber VARCHAR(255),
    SalesOrderLineNumber INT,
    RevisionNumber INT,
    OrderQuantity INT,
    UnitPrice DECIMAL(10,4),
    ExtendedAmount DECIMAL(10,4),
    UnitPriceDiscountPct DECIMAL(10,4),
    DiscountAmount DECIMAL(10,4),
    ProductStandardCost DECIMAL(10,4),
    TotalProductCost DECIMAL(10,4),
    SalesAmount DECIMAL(10,4),
    TaxAmt DECIMAL(10,4),
    Freight DECIMAL(10,4),
    CarrierTrackingNumber VARCHAR(255),
    CustomerPONumber VARCHAR(255),
    OrderDate DATETIME,
    DueDate DATETIME,
    ShipDate DATETIME
);
```

2. Import the 4 .CSV files that we downloaded from [this Dataset](https://docs.google.com/spreadsheets/d/17NYNRCWHbRI4mtDxCwamudDWDpzQuWk_1x0vsCINYZc/edit#gid=1921694854) into the corresponding tables created above using this query:
``` sql
LOAD DATA INFILE 'C:/Users/nicholas/OneDrive/Desktop/[filename].CSV' 
INTO TABLE DimCustomers 
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

## Exploratory Analysis With SQL
The schema **assignment** contains the 4 tables listed below, along with the shape of the data for each of the tables (# of rows & columns).
* DimCustomer - ***18484** rows, **29** columns* 
* DimProduct - ***569** rows, **24** columns*
* DimSalesTerritory - ***11** rows, **5** columns*
* FactResellerSales - ***60855** rows, **27** columns*

###### Query:
``` sql
-- Number of Columns in the dataset.
SELECT COUNT(*) AS NumberOfColumns 
FROM information_schema.columns
WHERE table_name = '[tablename]';
        
-- Number of Rows in the dataset.
SELECT COUNT(*) AS NumberOfRows
FROM [tablename];
```

Before conducting further analysis, assessing the quality of the data must be done first. This is done so for each table listed above. Data quality may be assessed based on these characteristics:
1. Completeness - If all necessary data is available, and whether there are missing data.
2. Uniqueness - Ensures only one instance of a data is happening and is not duplicated.
3. Validity - If data conforms to a specific format or business rule.
4. Consistency - If data stored in one table match relevant data stored elsewhere.

### **DimCustomer** 
##### Uniqueness 
- **CustomerKey** - Assuming this column is the Primary Key, then all values should be unique and not NULL. The query below counts all values as well as *distinct* (unique) values from this column. Both counts returning the same result of *18484* rows, hence having no duplicate values.
    * `SELECT COUNT(DISTINCT CustomerKey), COUNT(CustomerKey) FROM dimcustomer;`
- **CustomerAlternateKey** - All values in this column should also be unique as it is an alternate key. The query below counts all values as well as *distinct* values from this column. Both counts returning the same result of *18484* rows, hence having no duplicate values.
    * `SELECT COUNT(DISTINCT CustomerAlternateKey), COUNT(CustomerAlternateKey) FROM dimcustomer;`
- **EmailAddress** - Email addresses should also be unique for each unique customer, the query below returns the same result of *18484* rows.
    * `SELECT COUNT(DISTINCT EmailAddress), COUNT(EmailAddress) FROM dimcustomer;`

##### Validity
- **TotalChildren** & **NumberChildrenAtHome** - Assuming the *TotalChildren* refers to the total number of children each customer has, the value for *NumberChildrenAtHome* cannot exceed *TotalChildren* **(*TotalChildren* >= *NumberChildrenAtHome*)**
  * `SELECT * FROM assignment.dimcustomer WHERE TotalChildren >= NumberChildrenAtHome;`
- **MiddleName** - At a glance this column contains inconsistent values such as *"R"*, or *"R."*, or *"Radu"*. 
  * This query finds the most common naming format, ***"R"***:
    * `WITH MiddleNameLength AS ( SELECT MiddleName, LENGTH(MiddleName) AS NameLength FROM assignment.dimcustomer ) SELECT NameLength, COUNT(NameLength) AS Length_Count FROM MiddleNameLength GROUP BY NameLength;`
  * This query validates how many *MiddleName* contains the **correct format** containing only **1 capital letter** ranging from **A-Z**:
    * `SELECT MiddleName FROM assignment.dimcustomer WHERE MiddleName REGEXP '^[A-Z]{1}$';`
- **BirthDate** and **DateFirstPurchase** - These date columns has to be consistently formatted using the query below into ***YYYY-MM-DD*** format, as well as validates if *DateFirstPurchase* value is greater than *BirthDate*.
  * `SELECT CustomerKey, STR_TO_DATE(birthdate, '%d/%m/%Y') AS birthdate_formatted, STR_TO_DATE(datefirstpurchase, '%d/%m/%Y') AS datefirstpurchase_formatted FROM assignment.dimcustomer HAVING birthdate_formatted < datefirstpurchase_formatted;`
- **Phone** - Assuming there cannot be a duplicate phone number in the *Phone* column, unless it is in a different area code, using **GeographyKey** as the area code. 
  * The query below checks for how many ***Phone*** occurences happen in each *GeographyKey*.
    * `SELECT GeographyKey, Phone, COUNT(Phone) FROM assignment.dimcustomer GROUP BY GeographyKey, Phone;`
  * Then use this query to filter rows with only **one occurence** meaning **duplicate phone numbers** for that area is **excluded** and returns *14905* rows out of *18484*, meaning there are phone numbers that are duplicated even though it is in the same *GeographyKey*.
    * `SELECT GeographyKey, Phone, COUNT(Phone) FROM assignment.dimcustomer GROUP BY GeographyKey, Phone HAVING COUNT(phone) = 1;`

### **DimProduct**
##### Uniqueness 
- **ProductKey** - Assuming this column is the Primary Key, then all values should be unique and not NULL. The query below counts all values as well as *unique* values from this column. Both counts returning the same result of *569* rows, hence having **no duplicate values**.
    * `SELECT COUNT(ProductKey), COUNT(DISTINCT ProductKey) FROM assignment.dimproduct;`
- **ProductAlternateKey** - All values in this column should also be unique as it is an alternate key, the query below shows that there are only *488* distinct values in this column out of *569*, hence ***81* duplicate** values.
    * `SELECT COUNT(ProductAlternateKey), COUNT(DISTINCT ProductAlternateKey) FROM assignment.dimproduct;`

##### Validity 
- **FinishedGoodsFlag** - Assuming the value *"1"* marks finished goods (goods that have been completed by the manufacturing process which have not yet been sold to customers), products marked with *"0"* means it has not been sold yet and cannot contain a price.
  * The query below checks how many products are marked with "*0*" for the *FinishedGoodsFlag* column, and returns *209* rows.
    * `SELECT * FROM assignment.dimproduct WHERE FinishedGoodsFlag = 0;`
  * *ListPrice* and *DealerPrice* columns should be NULL for products with *"0"* value in the *FinishedGoodsFlag* column, since the product has not been sold yet. The query below checks and returns *0* rows meaning **all products are valid**, with *"0"* value in the *FinishedGoodsFlag* and does **not** have a *ListPrice* and *DealerPrice* column.
    * `SELECT FinishedGoodsFlag, ListPrice, DealerPrice FROM assignment.dimproduct WHERE FinishedGoodsFlag = 0 AND ListPrice IS NOT NULL AND DealerPrice IS NOT NULL;`
  * *DaysToManufacture* columns should also be = 0 for products with *"0"* value in *FinishedGoodsFlag*, since these products have not completed manufacturing. The query below checks how many **invalid** products are there based on this rule and returns *27* rows (**27 invalid Products**).
    * `SELECT FinishedGoodsFlag, DaysToManufacture FROM assignment.dimproduct WHERE FinishedGoodsFlag = 0 AND DaysToManufacture != 0;`
  * Similarly, *DaysToManufacture* columns should be > 0 for products with *"1"* value in *FinishedGoodsFlag*, since these products have completed manufacturing. 
   The query below checks how many **invalid** products are there based on this rule and returns *83* rows (***83* invalid Products**).
    * `SELECT FinishedGoodsFlag, DaysToManufacture FROM assignment.dimproduct WHERE FinishedGoodsFlag = 1 AND DaysToManufacture < 1;`

##### Completeness
- **StartDate** & **EndDate** - Some values within these columns appear to be missing (blank values), aside from explicit NULL values.
  * The query below **excludes** missing values in the *StartDate* & *EndDate* column, assuming missing values are *blank* or only contains only spaces. 
    It also formats the *StartDate* & *EndDate* column from the current ***YYYY-MM-DD hh:mm:ss*** format into ***YYYY-MM-DD*** format for consistency. Incorrect date values such as *00:00.0* will be formatted to NULL.
    * `SELECT DATE([StartDate or EndDate]) FROM assignment.dimproduct WHERE TRIM([StartDate or EndDate]) != '';`

### **DimSalesTerritory**
##### Validity
- Since there are only *10* records in this table, and all fields in this table such as *SalesTerritoryRegion*, *SalesTerritoryCountry*, *SalesTerritoryGroup* has the value of a region, we can remove invalid values that may not make sense.
The query below **excludes** rows where all 3 fields above have a value of *"NA"*.
    * `SELECT * FROM assignment.dimsalesterritory WHERE SalesTerritoryRegion != 'NA' AND SalesTerritoryCountry != 'NA'AND SalesTerritoryGroup !='NA';`

### **FactResellerSales**
##### Completeness
- There are **no missing or NULL values** within this table. *COUNT()* for all column names return the **correct amount** of *60855* rows.
    * `SELECT COUNT("columnname") FROM assignment.factresellersales;`

##### Consistency
- The query below tests if all **products** being sold in *FactResellerSales* are available in *DimProduct*. Only *54872* rows are returned out of *60855* meaning some products sold in *FactResellerSales* are not available in *DimProduct*.
    * `SELECT * FROM factresellersales f INNER JOIN DimProduct p ON f.ProductKey = p.productkey;`
- The query below tests if all **Sales Territories** in *FactResellerSales* are available in *DimSalesTerritory*. Correct number of *60855* rows are returned out of *60855* meaning all Sales Territories are available. 
    * `SELECT * FROM factresellersales f INNER JOIN dimsalesterritory d ON f.SalesTerritoryKey = d.SalesTerritoryKey;`

##### Validity
- *DiscountAmount* & *UnitPriceDiscountPct* column should be accurate based on *UnitPrice* column. **(*UnitPrice* x *UnitPriceDiscountPct* = *DiscountAmount*)**. 
The query below checks if there are any rows with an **incorrect** calculation for the *DiscountAmount*. It returns *3281 rows*, meaning these sales records have an **incorrect** *DiscountAmount*.
    * `SELECT UnitPrice, UnitPriceDiscountPct, DiscountAmount, (UnitPrice * UnitPriceDiscountPct) AS DiscountAmountCheck FROM assignment.factresellersales HAVING DiscountAmountCheck != discountamount;`
- Assuming *ExtendedAmount* refers to unit price multiplied by quantity of items purchased, the formula should be **(*UnitPrice* x *OrderQuantity* = *ExtendedAmount*)**. The query below checks if there are any rows with incorrect calculation for *ExtendedAmount*. It returns *0* rows, meaning **all** *ExtendedAmount* values are **correct**. 
    * `SELECT UnitPrice, OrderQuantity, ExtendedAmount, ROUND((UnitPrice * OrderQuantity), 4) AS ExtAmountCheck FROM assignment.factresellersales HAVING ExtendedAmount != ExtAmountCheck;`
- The rule for the 3 date fields *OrderDate*, *ShipDate*, *DueDate* should be as follows:
    ***OrderDate* <= *ShipDate* <= *DueDate***
    The query below checks if all rows meets this condition. It returns all *60855* rows meaning all values in **all 3** date fields above **meet** the condition.

### **Insights Observed**
- Customer Demographics from **DimCustomer** - 
    * Customer gender split is quite even, **51% Male** and **49% Female**.
        * `SELECT ROUND(SUM(Gender = 'M')/COUNT(*) * 100, 0) AS MalePct, ROUND(SUM(Gender = 'F')/COUNT(*) * 100, 0) AS FemalePct FROM assignment.DimCustomer;`
    * Roughly **68%** of Customers are homeowners.
        * `SELECT ROUND(SUM(HouseOwnerFlag =1)/COUNT(*)*100, 0) AS HouseOwnerPct FROM assignment.dimcustomer;`
    * Average yearly income for homeowners is **±58,000**, also having on average **2 children** per Customer.
        * `SELECT ROUND(AVG(YearlyIncome), 0) as AvgYearlyIncome, ROUND(AVG(TotalChildren), 0) AS AvgTotalChildren FROM assignment.dimcustomer WHERE HouseOwnerFlag = 1;`
    * Majority of customers **(75%)** are within the **Mid to High yearly income group (30,000 to 100,000)**.
      The percentage of customers owning a house and a car **correlate**s with their yearly income group. The **higher** the **income bracket**, the **more** customers that owns a house and a car. 
    An exception is in the *Very High Income* group where the % of customers owning a house and a car drops slightly the previous income group. (Query listed below:)
``` SQL
SELECT
    CASE
        WHEN yearlyincome < 30000 THEN 'Less than 30,000 (Low Income)'
        WHEN yearlyincome >= 30000 AND yearlyincome < 60000 THEN '30,000 to 59,999 (Middle Income)'
        WHEN yearlyincome >= 60000 AND yearlyincome <= 100000 THEN '60,000 to 100,000 (High Income)'
        ELSE 'Above 100,000 (Very High Income)'
    END AS YearlyIncomeGroup,
    COUNT(*) AS NumberOfCustomers,
	SUM(NumberCarsOwned) AS NumberCarsOwned,
	(SUM(CASE WHEN HouseOwnerFlag = 1 THEN 1 ELSE 0 END) / COUNT(CustomerKey) * 100) AS HouseOwnerPercentage,
    (SUM(CASE WHEN HouseOwnerFlag = 0 THEN 1 ELSE 0 END) / COUNT(CustomerKey) * 100) AS NonHouseOwnerPercentage
FROM
    assignment.DimCustomer
GROUP BY
YearlyIncomeGroup
ORDER BY
CASE
        WHEN YearlyIncomeGroup = 'Less than 30,000 (Low Income)' THEN 1
        WHEN YearlyIncomeGroup = '30,000 to 59,999 (Middle Income)' THEN 2
        WHEN YearlyIncomeGroup = '60,000 to 100,000 (High Income)' THEN 3
        ELSE 4
    END;
```
- Sales Insights from **FactResellerSales**, **DimProduct**, **DimSalesTerritory** -
    * **Top 3 sales periods** are during **January, February and May 2013**. (Products that are not in *DimProduct* are excluded)
        * `SELECT YEAR(OrderDate) AS OrderYear, MONTHNAME(OrderDate) AS OrderMonth, SUM(SalesAmount) AS SalesAmount FROM FactResellerSales f INNER JOIN DimProduct p ON f.ProductKey = p.ProductKey GROUP BY OrderMonth , OrderYear ORDER BY SalesAmount DESC LIMIT 3;`
    * **Top 5 products** in terms of sales (*SalesAmount*) within this period (*Jan, Feb, May 2013*) are:
    1. Mountain-200 Black, 38
    2. Touring-1000 Blue, 60
    3. Road-350-W Yellow, 48
    4. Mountain-200 Black, 42
    5. Touring-1000 Blue, 46
        * `SELECT ProductName, SUM(SalesAmount) AS SalesAmount FROM FactResellerSales f INNER JOIN DimProduct p ON f.ProductKey = p.ProductKey WHERE YEAR(OrderDate) = 2013 AND MONTH(OrderDate) IN (1,2,5) GROUP BY ProductName ORDER BY SalesAmount DESC; LIMIT 5`
    * **Top 3 regions** in terms of sales for *SalesTerritoryCountry*, *SalesTerritoryRegion*	*SalesTerritoryGroup* respectively, within this period (*Jan, Feb, May 2013*) are:
        1. United States, Southwest, North America
        2. Canada, Canada, North America
        3. United States, Northwest, North America
        * `SELECT SalesTerritoryCountry, SalesTerritoryRegion, SalesTerritoryGroup, SUM(SalesAmount) AS SalesAmount FROM FactResellerSales f INNER JOIN DimProduct p ON f.ProductKey = p.ProductKey INNER JOIN DimSalesTerritory d ON f.SalesTerritoryKey = d.SalesTerritoryKey WHERE YEAR(OrderDate) = 2013 AND MONTH(OrderDate) IN (1, 2, 5) GROUP BY SalesTerritoryCountry, SalesTerritoryRegion, SalesTerritoryGroup ORDER BY SalesAmount DESC LIMIT 3;`
    * Products are mostly ordered in dates towards the **end of the month**, such as **1st** and **28-31st**.
        * `SELECT DISTINCT DAY(orderdate) FROM FactResellerSales f INNER JOIN DimProduct p ON f.ProductKey = p.ProductKey ORDER BY DAY(orderdate) ASC;`

## SQL Questions
##### Question 1: What is the highest transaction of each month in 2012 for the product Sport-100 Helmet, Red?
###### Query:
``` sql
-- (1)
WITH Rank_Months as (SELECT
        -- (2)
        f.OrderDate,
        f.SalesAmount,
        -- (3)
        DENSE_RANK() OVER(PARTITION BY MONTH(f.OrderDate) ORDER BY f.SalesAmount DESC) as month_ranked
    FROM FactResellerSales AS f
    INNER JOIN DimProduct AS d 
    ON f.ProductKey = d.ProductKey
    -- (4)
    WHERE 
        d.ProductName = 'Sport-100 Helmet, Red' AND
        YEAR(f.OrderDate) = 2012)
-- (5)
SELECT 
	DATE_FORMAT(OrderDate, '%m-%Y') as Month,
    ROUND(SalesAmount, 1) as SalesAmount,
    DATE(OrderDate) as OrderDate
FROM Rank_Months
-- (6)
WHERE month_ranked = 1;
```
###### Explanation:
1. Firstly, a **Common Table Expression (CTE)** named *'Rank_Months'* is created to retrieve relevant data.
2. The CTE **selects** columns *f.OrderDate*, *f.SalesAmount* from the tables *FactResellerSales* and *DimProduct*. 
   The tables are joined using the **INNER JOIN** statement to ensure only rows with matching *ProductKey* from both tables are included.
3. A **DENSE_RANK()** function named *month_ranked* is also created in the CTE to assign a **rank** to each row of each month within *f.OrderDate*, based on the **sales amount** in *f.SalesAmount* sorted in **descending order**. 
4. A **WHERE** clause is created to define two conditions for the query:
   * `d.ProductName = 'Sport-100 Helmet, Red'` ensures that only rows with this specific product are selected.
   * `YEAR(f.OrderDate) = 2012` filters the data to include only orders made in the year 2012.
5. After creating the CTE, the **main query** is executed. It retrieves 3 columns from the CTE above.
   * `DATE_FORMAT(OrderDate, '%m-%Y') as Month` formats ***MM-YYYY*** from the OrderDate column to create the **Month** column.
   * `ROUND(SalesAmount, 1) as SalesAmount` column represents the (highest) **Sales Amount** of each Month (rounded to 1 decimal place).
   * `DATE(OrderDate) as OrderDate` formats ***YYYY-MM-DD*** from the OrderDate column to create the **OrderDate** column.
6. Lastly, the **WHERE** clause `WHERE month_ranked = 1` filters the results from the CTE. 
   It only includes rows where the *month_ranked* value is equal to *1*, meaning that only rows with the **highest sales amount (ranked as 1)** for each month are included.

##### Question 2: Find all the products and their total sales amount by month. Only consider products that had at least one sale in 2012.
###### Query:
```sql
-- (1)
WITH valid_products as (
        SELECT DISTINCT ProductKey
        FROM FactResellerSales
        WHERE YEAR(OrderDate) = 2012
		AND SalesAmount > 0
    )
-- (2)
SELECT
    p.ProductName,
    ROUND(SUM(f.SalesAmount), 1) AS SalesAmount,
    DATE_FORMAT(f.OrderDate, '%m-%Y') AS Month    
FROM
-- (3)
    FactResellerSales AS f
INNER JOIN
     valid_products ON f.ProductKey = valid_products.ProductKey
-- (4)
INNER JOIN
    DimProduct AS p ON valid_products.ProductKey = p.ProductKey
-- (5)
GROUP BY
    p.ProductName, Month
-- (6)
ORDER BY
    p.productname ASC, DATE(f.OrderDate) ASC;
```
###### Explanation:
1. Firstly, a **Common Table Expression (CTE)** named *'valid_products'* is created to retrieve *ProductKey* of products sold in *2012* that at least have one sale. The *ProductKey* will be used for reference in a join later.
2. Next, the **main query** selects the required columns:
   * *p.ProductName* to define the **product**.
   * `ROUND(SUM(f.SalesAmount), 1) AS SalesAmount` to define the **total sales amount** for **each month** (rounded to 1 decimal place).
   * `DATE_FORMAT(f.OrderDate, '%m-%Y') as Month` to define and format the **Month** to **MM-YYYY** format.
3. The columns are selected from the *FactResellerSales* table, which is joined with the CTE *valid_products* using an **INNER JOIN** clause to ensure only rows with matching *ProductKey* from both tables are included. 
This means that products that are sold in *factresellersales* in **any month** and **any year** are included, as long as it has at least one sale in the year *2012*.
4. Another **INNER JOIN** is performed with the *DimProduct* table to get the *ProductName*.
5. A **GROUP BY** statement is performed to **group** the **total sales** by **products**, for each **month**.
6. Lastly, the results are **sorted** according to products name and months in ascending order.

##### Question 3: What are the age groups of customers categorised by marital status and gender?
###### Query: 
``` sql
-- (1)
WITH Find_Age as (SELECT
    MaritalStatus,
    Gender,
    YEAR(CURRENT_DATE) - YEAR(str_to_date(BirthDate, '%d/%m/%Y')) - (DATE_FORMAT(CURRENT_DATE, '%m%d') < DATE_FORMAT(str_to_date(BirthDate, '%d/%m/%Y'), '%m%d')) AS Age
FROM
    dimcustomer)
-- (2)
select MaritalStatus, Gender, 
    SUM(CASE WHEN Age < 35 THEN 1 ELSE 0 END) AS AgeBelow35,
    SUM(CASE WHEN Age BETWEEN 35 AND 50 THEN 1 ELSE 0 END) AS AgeBetween35and50,
    SUM(CASE WHEN Age > 50 THEN 1 ELSE 0 END) AS AgeAbove50
from Find_Age
-- (3)
GROUP BY
    MaritalStatus,
    Gender
-- (4)
ORDER BY
    MaritalStatus,
    Gender;
```
###### Explanation: 
1. Firstly, a **Common Table Expression (CTE)** named *'Find_Age'* is created to get the needed columns:
   * MaritalStatus
   * Gender
   * Age
     * To find the *Age*, we need to substract the current year `YEAR(CURRENT_DATE)` with the year from the customer's *BirthDate* `YEAR(str_to_date(BirthDate, '%d/%m/%Y'))`.
     * To make sure the *Age* is accurate, we add another check to see if the customer's birthday has occured yet this year using `(DATE_FORMAT(CURRENT_DATE, '%m%d') < DATE_FORMAT(str_to_date(BirthDate, '%d/%m/%Y'), '%m%d'))`. 
     This formats the current date into **month%date%** (i.e. *09 August, 2023* &rarr; *0809*), as well as the customer's birthdate from the current *DD/MM/YYYY* also into **month%date%** format.
     * For **example**, a customer's BirthDate is on **11/11/1996**.
     **[Current Year - BirthDate Year]** 2023-1996 = 27
     **[Check if birthdate have passed as of 9 August, 2023]** 0809 < 1111. Since the formatted current date 0809 is lower than the formatted BirthDate 1111, that means the birthday haven't passed yet. 
    Based on this calculation `(DATE_FORMAT(CURRENT_DATE, '%m%d') < DATE_FORMAT(str_to_date(BirthDate, '%d/%m/%Y'), '%m%d'))` would return 1 as True (or 0 when False/birthday already passed).

        The final calculation would be [Current Year - BirthDate Year - 1 ] = 2023 - 1996 - 1 = **26 Years Old**
2. Next the main query selects the columns *MaritalStatus* and *Gender*, and use 3 **CASE** statements to classify the 3 age groups based on the *Age* column derived from the CTE above. 
   * Each CASE statements define a rule to **increment a value by 1** if the condition is met (i.e. Age < 35) and **sums** all the values together under a column (i.e. *AgeBelow35*). This is equivalent to **counting** how many customers **fits each age groups**.
3. A **GROUP BY** statement is performed to **group** the **number of customers** by **MaritalStatus** and **Gender** for each age group columns.
4. Lastly, the results are **sorted** according to MaritalStatus and Gender.

##### Question 4: What is the combination of product & country with the lowest amount of sales per month in 2012?
###### Query:
```sql
-- (1)
SELECT 
    DATE_FORMAT(f.OrderDate, '%m-%Y') AS Month,
    s.SalesTerritoryCountry,
    p.ProductName,
    MIN(f.SalesAmount) AS SalesAmount
-- (2)
FROM
    FactResellerSales f
        INNER JOIN
    DimProduct p ON f.ProductKey = p.ProductKey
        INNER JOIN
    DimSalesTerritory s ON f.SalesTerritoryKey = s.SalesTerritoryKey
-- (3)
WHERE
    YEAR(f.OrderDate) = 2012
-- (4)
GROUP BY MONTH(f.OrderDate) , s.SalesTerritoryCountry , p.ProductName
-- (5)
ORDER BY SalesAmount ASC;
```

###### Explanation:
1. Firstly, the relevant columns are selected:
    * `DATE_FORMAT(f.OrderDate, '%m-%Y') AS Month` formats the *OrderDate* column into ***MM-YYYY*** format.
    * SalesTerritoryCountry
    * ProductName
    * `MIN(f.SalesAmount) AS SalesAmount` retrieves the **minimum** *SalesAmount*
2. Columns are selected from the tables FactResellerSales which joins to 2 tables *DimProduct* and *DimSalesTerritory* using the **INNER JOIN** clause, to ensure combine only matching values in a field common to both joined tables are retrieved.
    * *FactResellerSales* joins with *DimProduct* table to retrieve the *ProductName* field.
    * *FactResellerSales* joins with *DimSalesTerritory* to retrieve the *SalesTerritoryCountry* field.
3. The **WHERE** clause `YEAR(f.OrderDate) = 2012` filters the results to only include rows where the year of the *OrderDate = 2012*.
4. The **GROUP BY** *MONTH(f.OrderDate)*, *s.SalesTerritoryCountry*, *p.ProductName* put each row with the same pair of *Sales*, and *ProductName* are in the same group, and then calculate the aggregate function **MIN(f.SalesAmount)** to find the **lowest SalesAmount per Month** for each of these groups for each *OrderDate Month*.
5. Lastly, the results are **sorted** in ascending order by *SalesAmount*.
