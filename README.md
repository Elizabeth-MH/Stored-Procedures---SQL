# Stored-Procedures---SQL
Stored Procedures - SQL


CREATE the Following Stored Procedures
Note: Make sure to execute and test each stored procedure after creation in order to assure that it is running properly. Also, aggregates will be needed to calculate the proper results for your result set. Meaning a Group By clause will be needed.
Database = Adventureworks

Name: CREATE PROCEDURE proc_TerritorySalesByYear
a. Parameter: OrderYear
(Passing Value)
b. Display the Total sales by territory for the Year Parameter
(The following is for the results set, which will be created in your statement in order to pass your parameter to receive the total sales by each territory)
--HardCode Component Test--Total Sales for Territory 5 in 2011

SELECT  SUM (TotalDue) as TotalSales,TerritoryID
--SELECT *
FROM  [AdventureWorks2019].[Sales].[SalesOrderHeader]
WHERE TerritoryID = 5 AND OrderDate BETWEEN '2011-01-01' AND '2011-12-31'
GROUP BY TerritoryID
*/

 CREATE PROC proc_TerritorySalesByYear
	   @StartDate DATE,
	   @EndDate DATE,
	   @TerritoryID INT

 AS
 BEGIN

	SET NOCOUNT ON;
 
	 SELECT		SUM (TotalDue) as TotalSales,TerritoryID
	 FROM		[AdventureWorks2019].[Sales].[SalesOrderHeader]
	 WHERE		TerritoryID = @TerritoryID AND OrderDate BETWEEN @StartDate AND @EndDate-- Startdate AND EndDate
	 GROUP BY   TerritoryID

 END
 
--RUN the Stored Procedure

 EXEC proc_TerritorySalesByYear '2011-01-01','2011-12-31',4 --Yields Total Sales of $3,144,713.0989 
/*
2. Name: CREATE PROCEDURE proc_SalesByTerritory
a. Parameter: Territory Name
(Passing Value)
b. Results set: Display Total sales by Year for the Territory Name Parameter
(The following is for the results set, which will be created in your statement in order to pass your parameter to receive the total sales by each year)

--HardCode Component Test

SELECT  SUM (a.TotalDue) as TotalSales,b.Name
--SELECT *
FROM				[AdventureWorks2019].[Sales].[SalesOrderHeader] a
LEFT OUTER JOIN	    [AdventureWorks2019].[Sales].[SalesTerritory] b
ON                  a.TerritoryID = b.TerritoryID
WHERE               b.Name = 'Northwest ' AND OrderDate BETWEEN '2011-01-01' AND '2011-12-31'
GROUP BY            b.Name
*/

CREATE PROC proc_SalesByTerritory

		@TerritoryName VARCHAR (20),
		@StartDate DATE,
		@EndDate DATE

AS
 BEGIN
   SET NOCOUNT ON;

		SELECT           SUM (a.TotalDue) as TotalSales,b.Name
		FROM             [AdventureWorks2019].[Sales].[SalesOrderHeader] a
		LEFT OUTER JOIN  [AdventureWorks2019].[Sales].[SalesTerritory] b
		ON               a.TerritoryID = b.TerritoryID
		WHERE            b.Name =@TerritoryName AND OrderDate BETWEEN @StartDate AND @EndDate
		GROUP BY         b.Name

END

--RUN the Stored Procedure

EXEC proc_SalesByTerritory 'Northwest','2011-01-01','2011-12-31'--Yields Total Sales of $2,620,943.826
/*
3.Name: CREATE PROCEDURE proc_TerritoryTop5Sales_ByProduct
a. Parameter: Territory Name
(Passing Value)
b. Results set: Top 5 Products by year
(The following is for the results set, which will be created in your statement in order to pass in Territory Name to receive the Top 5 Products sold (Sum of Line Total) by each Year)

-- Hardcode Component

	SELECT           TOP(5)c.ProductID,SUM(c.OrderQty) AS TotalQuantity,d.Name
    FROM             [AdventureWorks2019].[Sales].[SalesTerritory] a
    LEFT OUTER JOIN  [AdventureWorks2019].[Sales].[SalesOrderHeader] b
    ON               a.TerritoryID  = b.TerritoryID       
    LEFT OUTER JOIN  [AdventureWorks2019].[Sales].[SalesOrderDetail] c
    ON               b.SalesOrderID =c.SalesOrderID
	LEFT OUTER JOIN  [AdventureWorks2019].[Production].[Product] d
	ON				 c.ProductID =d.ProductID
	WHERE            a.Name = 'Canada' AND  OrderDate BETWEEN '2011-01-01' AND '2011-12-31' 
	Group BY         c.ProductID,d.Name
	ORDER BY         TotalQuantity DESC
*/

CREATE PROCEDURE proc_TerritoryTop5Sales_ByProduct
		   @TerritoryName VARCHAR (25),
		   @StartDate DATE,
		   @EndDate DATE

AS
BEGIN
	  SET NOCOUNT ON; 
       
		SELECT           TOP(5)c.ProductID,SUM(c.OrderQty) AS TotalQuantity,d.Name
		FROM             [AdventureWorks2019].[Sales].[SalesTerritory] a
		LEFT OUTER JOIN  [AdventureWorks2019].[Sales].[SalesOrderHeader] b
		ON               a.TerritoryID  = b.TerritoryID       
		LEFT OUTER JOIN  [AdventureWorks2019].[Sales].[SalesOrderDetail] c
		ON               b.SalesOrderID =c.SalesOrderID
		LEFT OUTER JOIN  [AdventureWorks2019].[Production].[Product] d
		ON				 c.ProductID =d.ProductID
		WHERE            a.Name =@TerritoryName AND  OrderDate BETWEEN @StartDate AND @EndDate 
		Group BY         c.ProductID,d.Name
		ORDER BY         TotalQuantity DESC


END

--RUN
EXECUTE proc_TerritoryTop5Sales_ByProduct 'Canada','2012-01-01','2012-12-31'

/*
4. Stored Procedure with Output Parameters

  a. Add a MgrID column to your emp table. ADDED BELOW
  b. Populate it accordingly using the integer data type and same number of characters as the empID column-ADDED BELOW
*/

--. This will be the “emp” table (i.e., Employee Table, or dbo.emp). It will have three columns: Empid / empname / deptid and the corresponding data types will be int / varchar(50) / int, respectively. After you have created this table, click the Save icon and close.

 	CREATE TABLE	Employee( EmpID INT, EmpName VARCHAR (50),Salary MONEY ,DeptID INT,MgrID INT--AddingMgrIDColumn)
    INSERT INTO		Employee( EmpID,EmpName,Salary,DeptID,MgrID)
    VALUES			(1,'John Doe',70000, 5,1),
					(2,'Jane Doe',85000,7,2) ,
					(5,'Peggy Sue',67000, 9,3)
SELECT * FROM Employee

/*
c. Build a SP that passes in empID and returns an output parameter of the mgrID - (Create the SP and verify it works correctly)
(Keep in mind that the mgrID will also be an individual’s empID., since managers are also employees)

Hardcode Component
SELECT MgrID
FROM Employee
WHERE EmpID = 5
*/
--Stored Procedure

CREATE PROC proc_ManagerIDByEmpID
		@MgrID INT

AS
BEGIN 
	SET NOCOUNT ON;

	 SELECT	 MgrID
	 FROM	 Employee
	 WHERE	 EmpID = @MgrID

END

EXECUTE proc_ManagerIDByEmpID 1 --MGR ID =1
--d. (Start a New Query and separate it from the previous Stored Procedure) Declare an empid int and manager_name Varchar(50) variable

DECLARE	@ManagerName VARCHAR(50),@EmpID INT

 SET	   @ManagerName=
                       (				
			 SELECT		EmpName as ManagerName
			 FROM		Employee
			 WHERE		EmpID =@EmpID
                         )
 SELECT	    @ManagerName
/*
e. Hard code your new empid variable and Pass it into your new SP to return the mgrid
SELECT EmpName as ManagerName
FROM Employee
WHERE EmpID =5
*/

--(Use an actual empid in the variable location to test and Pass it into your new SP to return the mgrID)

DECLARE		@EmpID INT
EXECUTE	    proc_ManagerIDByEmpID 5
SELECT	    @EmpID
/*
f. Capture that mgrid in a variable and use that mgrid variable to determine the Managers name
(Create another statement which locates the Manager’s Name by using mgrID)

-Hardcode component

 SELECT EmpName as ManagerName
 FROM Employee
 WHERE MgrID =3
*/

 CREATE PROC proc_ManagerNameByID
		@MgrID INT

AS
BEGIN 
	SET NOCOUNT ON;

	 SELECT EmpName as ManagerName
	 FROM Employee
	 WHERE MgrID = @MgrID

END
--e. Print the Managers name

DECLARE	   @MgrID INT
EXECUTE	   proc_ManagerNameByID 3
SELECT	   @MgrID
PRINT      @MgrID 
GO

/*

---------------------------------------------------------------------LAB TWO --------------------------------------------------------------------------------

Example 1.0.
TableA TableB
Field1 Field1
1 2
2 5
3 7
4 6
4 3
5 3
6 9
Write SQL queries below based on table layout in Example 1.0.
*/
--1. Display data from TableA where the values are identical in TableB.

SELECT       Field1
FROM         TableA 
INNER JOIN	 TableB
ON           TableA.Field1 = TableB.Field1
--2. Display data from TableA where the values are not available in TableB.

SELECT       Field1
FROM         TableA 
LEFT JOIN	 TableB
ON           TableA.Field1 = TableB.Field1
--3. Display data from TableB where the values are not available in TableA.

SELECT       Field1
FROM         TableA 
RIGHT JOIN	 TableB
ON           TableA.Field1 = TableB.Field1
/*
4.Create a Stored Procedure that passes in the SalesOrderID as a parameter. This stored procedure will return the SalesOrderID, the Date of the transaction and a count of how many times the item was purchased.

SELECT	SalesOrderID,TransactionDate, COUNT (Transactions)
FROM    Table
WHERE   SalesOrderID =3
*/

CREATE PROCEDURE Proc_TransactionCntByDateFromSalesOrderID
 
	@SalesOrderID INT

AS
 BEGIN
	SET NOCOUNT ON;
	SELECT	SalesOrderID,TransactionDate, COUNT (Transactions)
	FROM    Sales.SalesOrderHeader
	WHERE   SalesOrderID =@SalesOrderID

GO
/*
5.Create a Stored Procedure that passes in the SalesOrderID as a parameter. This stored procedure will return the SalesOrderID, Date of the transaction, shipping date, City and State.

HardCode Logic
SELECT SalesOrderID,TransactionDate,ShipDate,City, State
FROM Table
WHERE SalesOrderID =5
*/

CREATE PROCEDURE Proc_SalesorderIDTransInfoFromSalerOrderID
	   @SalesOrderID INT

AS

 BEGIN
   SET NOCOUNT ON;

	SELECT	           a.SalesOrderID,a.OrderDate TransactionDate,a.ShipDate,b.City, c.StateProvinceCode State
	FROM			   [AdventureWorks2019].[Sales].[SalesOrderHeader] a
	LEFT OUTER JOIN	   [AdventureWorks2019].[Person].[Address] b
	ON                 a.ShipToAddressID   = b.AddressID
	LEFT OUTER JOIN    [AdventureWorks2019].[Person].[StateProvince] c
	ON                 b.StateProvinceID = c.StateProvinceID
	WHERE              SalesOrderID = @SalesOrderID

END
/*
6.Create a stored procedure that passes in the Territory Name as a parameter. This stored procedure will return the Territory Group, CountryRegionCode, Count of SalesHeaders in 2001, and the Count of SalesDetails in 2001

Hardcode Logic
SELECT TerritoryGroup,CountryRegionCode,COUNT(SalesHeader),COUNT(SalesDetails)
FROM Table
WHERE Name =@TerritoryName AND OrderDate BETWEEN @StartDate AND @EndDate--2001
GROUP BY TerritoryGroup,CountryRegionCode,COUNT(SalesHeader),COUNT(SalesDetails)
*/

CREATE PROC Proc_TerritorySalesDetailsByGroup
		@Name VARCHAR (25)
AS 
BEGIN
  SET NOCOUNT ON;
		SELECT		    a.Group AS TerritoryGroup, a.CountryRegionCode,COUNT(b.SalesOrderID) AS SalesOrderCnt,COUNT(c.SalesOrderDetailID) AS SalesOrderDetailCnt
		FROM		    [AdventureWorks2019].[Sales].[SalesTerritory] a
		LEFT JOIN		[AdventureWorks2019].[Sales].[SalesOrderHeader] b
		ON			    a.TerritoryID  = b.TerritoryID
		LEFT JOIN		[AdventureWorks2019].[Sales].[SalesOrderDetail] c
		ON			    b.SalesOrderID  = c.SalesOrderID
		WHERE   	    a.Name =@Name
		GROUP BY	    a.Group,a.CountryRegionCode

 END
/*
7.Create a stored procedure that passes in the Product name as a parameter. This stored procedure will return the lowest price in History, Highest Price in History, difference between the two prices, Count of SalesDetails and the Sum of LineTotal.

HardCode Logic
SELECT LowestPrice , Highest Price,(HighestPrice-LowestPrice)AS PriceDifference,COUNT(SalesDetails) ,SUM (LineTotal)
FROM TABLE
WHERE ProductName = Y
GROUP BY LowestPrice , Highest Price,(HighestPrice-LowestPrice)AS PriceDifference,COUNT(SalesDetails) ,SUM (LineTotal)
*/

CREATE PROC  Proc_PriceHistoryfromName
			 @Name VARCHAR (25)
 AS
 BEGIN
   SET NOCOUNT ON;

		 SELECT		c.Name, MIN (b.UnitPrice ) AS LowestPrice,MAX (b. UnitPrice ) AS  HighestPrice ,MAX (b.UnitPrice)- MIN (b.UnitPrice) PriceDifference,COUNT (b.SalesorderDetailID) CntSalesOrderDetailID,SUM (b.LineTotal)SumOfLineTotal
		 FROM	    [AdventureWorks2019].[Production].[ProductListPriceHistory] a
		 LEFT JOIN	[AdventureWorks2019].[Sales].[SalesOrderDetail]b
		 ON		    a.ProductID = b.ProductID
		 LEFT JOIN  [AdventureWorks2019].[Production].[Product] c
		 ON         b.ProductID = c.ProductID
		 WHERE      c. Name =@Name
		 GROUP BY    c.ProductID,c.Name,b.UnitPrice,b.SalesOrderDetailID,b.LineTotal 

 END
/*
8.Create a SP that passes in the OrderYear as a parameter. This stored procedure will return the Count of the SalesHeaders and SalesDetails for the OrderYear parameter.
Hardcode logic
SELECT COUNT(SalesHeaders), COUNT ( SalesDetails)
FROM TABLE
WHERE OrderYear
GROUP BY COUNT(SalesHeaders), COUNT ( SalesDetails)
*/

CREATE PROC Proc_SalesCntByYear
@StartDate DATE,
@EndDate DATE
AS
BEGIN
SET NOCOUNT ON;

 SELECT		 COUNT (a.SalesOrderID),COUNT (b.SalesOrderDetailID)
 FROM		[AdventureWorks2019].[Sales].[SalesOrderHeader] a
 LEFT JOIN	[AdventureWorks2019].[Sales].[SalesOrderDetail] b
 ON			a.SalesOrderID =b.SalesOrderID
 WHERE		a.OrderDate BETWEEN @StartDate AND @EndDate
 GROUP BY	a.SalesOrderID,b.SalesOrderDetailID
 
END
