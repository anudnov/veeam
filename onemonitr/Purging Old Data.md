#### How to reduce Veeam ONE Database size

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
