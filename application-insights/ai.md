# Application Insights

# Query traces = LogInformation

When logging information at the backend code with `logger.LogInformation` it will be available via Application Insights traces.
```
traces | where message startswith "Search string to look for.." | order by timestamp desc
```
It is possible to group-by alsi for advanced queries:
```
traces | where operation_Name == 'Receiver' | where message startswith "Upserted the Entity" | order by timestamp desc ;

traces | where operation_Name == 'Receiver' | where message startswith "Processing Entity" | order by timestamp desc ;

traces | where operation_Name == 'Receiver' | where message startswith "Processing Entity" | where customDimensions.prop__actionCode == 'Add' | order by timestamp desc ;

traces | where operation_Name == 'Receiver'| where message startswith "Processed messageId: " | order by timestamp desc
| summarize count() by tostring(customDimensions.prop__rootName)

```
