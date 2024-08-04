## How to reduce Veeam ONE Database size

#### Step 1.
* Stop services:
```
stop-service VeeamDCS
stop-service VeeamRSS
stop-service VeeamCRS
```
* OR
```
Get-Service Veeam* | Stop-Service
Get-Service Veeam* | Start-Service
```
#### Step 2.
* Backup your Veeam ONE SQL database
 ```
TO  DISK = N'D:\veeamone.bak' WITH NOFORMAT, NOINIT,  NAME = N'VeeamOne-Full Database Backup', SKIP, NOREWIND, NOUNLOAD,  STATS = 10 
GO
```
#### Step 3, cleaning:
* Open the SQL Server Management Studio

```
USE [VeeamOne]
GO
SELECT * from monitor.Event
GO
```
```
USE [VeeamOne]
GO
DELETE from monitor.Event
GO
```
* Get size of all tables in database
```
SELECT 
    t.NAME AS TableName,
    s.Name AS SchemaName,
    p.rows,
    SUM(a.total_pages) * 8 AS TotalSpaceKB, 
    CAST(ROUND(((SUM(a.total_pages) * 8) / 1024.00), 2) AS NUMERIC(36, 2)) AS TotalSpaceMB,
    SUM(a.used_pages) * 8 AS UsedSpaceKB, 
    CAST(ROUND(((SUM(a.used_pages) * 8) / 1024.00), 2) AS NUMERIC(36, 2)) AS UsedSpaceMB, 
    (SUM(a.total_pages) - SUM(a.used_pages)) * 8 AS UnusedSpaceKB,
    CAST(ROUND(((SUM(a.total_pages) - SUM(a.used_pages)) * 8) / 1024.00, 2) AS NUMERIC(36, 2)) AS UnusedSpaceMB
FROM 
    sys.tables t
INNER JOIN      
    sys.indexes i ON t.OBJECT_ID = i.object_id
INNER JOIN 
    sys.partitions p ON i.object_id = p.OBJECT_ID AND i.index_id = p.index_id
INNER JOIN 
    sys.allocation_units a ON p.partition_id = a.container_id
LEFT OUTER JOIN 
    sys.schemas s ON t.schema_id = s.schema_id
WHERE 
    t.NAME NOT LIKE 'dt%' 
    AND t.is_ms_shipped = 0
    AND i.OBJECT_ID > 255 
GROUP BY 
    t.Name, s.Name, p.Rows
ORDER BY 
    TotalSpaceMB DESC, t.Name

```
* For example, delete all from the table "PerfSampleDay4", older then "2024-07-10 00:00:00.000":
```
DELETE FROM [VeeamONE].[monitor].[PerfSampleDay4]
WHERE timestamp < '2024-07-10 00:00:00.000';
```
*  Shrink the Database
```
USE [VeeamONE]
GO

DBCC SHRINKDATABASE ([VeeamONE]);
```
*  Start services:
```
stop-service VeeamDCS
stop-service VeeamRSS
stop-service VeeamCRS
```
```

```
