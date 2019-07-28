
## normalize vs denormalize
- Nomalization is eliminating the redundant data. RDBMS is nomalization. Normalization is for write-heavy.
- Denormalization allows redundant data. Nosql is denornalization. Denormalization is for read-heavy.

## Nosql vs RDBMS
- RDBMS is generated when storage and memory are very expensive, so it try not to keep any redundant data. But it is not good on speed and scaling.
- Nosql is generated for better speed and scaling. 
- RDBMS supports transaction. Nosql has limitaition on that.  
- RDBMS data stores in tables while nosql data is saved as document or key/value pair.
- 

## RDBMS ACID


## SQL SERVER lock escalation
```
select name, lock_escalation_desc
from sys.tables
```
https://dotnetvibes.com/2017/08/22/lock-escalations-in-sql-server/

## make sql server available for jdbc connecting
There are a lot of articles and videos talking about enable TCP/IP. But none of them mentioned in the sql server studio, you need right click the database -> security then -> Server authentication, you need select "SQL Server and Windows authentication mode"

