```
Get-VBRJob
```
```
Get-VBRJob | Format-Table -Property Name, Type, Description, LastResult, ScheduleOptions
```
```
Get-VBRJob | Select-Object Name, Type, Description, LastResult, ScheduleOptions | Export-Csv -Path "C:\temp\output.csv" -NoTypeInformation
```
