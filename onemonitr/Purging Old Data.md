## How to reduce Veeam ONE Database size

#### Step 1.
```
stop-service VeeamDCS
stop-service VeeamRSS
stop-service VeeamCRS
```
OR
```
Get-Service Veeam* | Stop-Service
Get-Service Veeam* | Start-Service
```
#### Step 2.
 Backup your Veeam ONE SQL database
TO  DISK = N'D:\veeamone.bak' WITH NOFORMAT, NOINIT,  NAME = N'VeeamOne-Full Database Backup', SKIP, NOREWIND, NOUNLOAD,  STATS = 10 
GO

#### Step 3.
Cleaning:
Open the SQL Server Management Studio

```
USE [VeeamOne]
GO
SELECT * from monitor.Event
GO
```
