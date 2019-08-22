
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

## mssql server lock
Query to view the lock
```
SELECT dm_tran_locks.request_session_id,
       dm_tran_locks.resource_database_id,
       DB_NAME(dm_tran_locks.resource_database_id) AS dbname,
       CASE
           WHEN resource_type = 'OBJECT'
               THEN OBJECT_NAME(dm_tran_locks.resource_associated_entity_id)
           ELSE OBJECT_NAME(partitions.OBJECT_ID)
       END AS ObjectName,
       partitions.index_id,
       indexes.name AS index_name,
       dm_tran_locks.resource_type,
       dm_tran_locks.resource_description,
       dm_tran_locks.resource_associated_entity_id,
       dm_tran_locks.request_mode,
       dm_tran_locks.request_status
FROM sys.dm_tran_locks
LEFT JOIN sys.partitions ON partitions.hobt_id = dm_tran_locks.resource_associated_entity_id
LEFT JOIN sys.indexes ON indexes.OBJECT_ID = partitions.OBJECT_ID AND indexes.index_id = partitions.index_id
WHERE resource_associated_entity_id > 0
  AND resource_database_id = DB_ID()
 and request_session_id=74
ORDER BY request_session_id, resource_associated_entity_id
```
https://www.cnblogs.com/CoderAyu/p/11375088.html