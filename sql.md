
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

