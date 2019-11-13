
## mssql OBJECT_ID
check if one object exits
```
IF OBJECT_ID('dbo.[CK_ConstraintName]', 'C') IS NOT NULL 
    ALTER TABLE dbo.[tablename] DROP CONSTRAINT CK_ConstraintName
```
the second parameter is constraint object types
```
C = CHECK constraint
D = DEFAULT (constraint or stand-alone)
F = FOREIGN KEY constraint
PK = PRIMARY KEY constraint
R = Rule (old-style, stand-alone)
UQ = UNIQUE constraint
```
https://stackoverflow.com/questions/2499332/how-to-check-if-a-constraint-exists-in-sql-server

## WTIH... SELECT
when creating complicated sub queries join, it is easy to do it using "WITH"
```
WITH cte
AS (col1, col2...)  
(
    SELECT id
        ,product
        ,order_id
        ,ROW_NUMBER() OVER (
            PARTITION BY id ORDER BY Order_Date ASC
            ) AS order_No
    FROM orders
    )
SELECT c.id
    ,c.country
    ,c1.Product
    ,c1.order_id
FROM customers c
INNER JOIN cte c1 ON c.id = c1.id
WHERE c1.order_No = 1
```
https://docs.microsoft.com/en-us/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-2017

## random number for each row
```
update TOTALS 
set AMT_CDT_DR_SET=ABS(CHECKSUM(NewId())) % 10000,
AMT_CDT_CR_SET=ABS(CHECKSUM(NewId())) % 10000,
AMT_CDT_DR_UNSET=ABS(CHECKSUM(NewId())) % 10000,
AMT_CDT_CR_UNSET=ABS(CHECKSUM(NewId())) % 10000
WHERE DATE_PROC>'2019-10-05' AND DATE_PROC<=DATEADD(DAY, 3, '2019-10-05');
```
https://stackoverflow.com/questions/1045138/how-do-i-generate-random-number-for-each-row-in-a-tsql-select  

