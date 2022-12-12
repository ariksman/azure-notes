## <img src="azureApplicationInsights.png" width="60" />Application Insights

## Query traces = LogInformation

When logging information at the backend code with `logger.LogInformation` it will be available via Application Insights **traces** if we have configured the `SeriLog` to do this.

``` CSharp
internal static void InitializeLogger(this LoggerConfiguration loggerConfiguration, WebHostBuilderContext hostingContext)
{
    var serilogSettings = new SerilogSettings(hostingContext.Configuration);
    loggerConfiguration
        .ReadFrom.Configuration(hostingContext.Configuration)
        .Enrich.FromLogContext()
        .Enrich.WithProperty("Application", "Gateway.Api")
        .WriteTo.Console(theme: SystemConsoleTheme.Colored)
        .WriteTo.Trace()
        .WriteTo.ApplicationInsights(serilogSettings.InstrumentationKey, TelemetryConverter.Traces);
}
```

``` Kusto
traces | where message startswith "Search string to look for.." | order by timestamp desc
```
It is possible to group-by alsi for advanced queries:
``` Kusto
traces | where operation_Name == 'Receiver' | where message startswith "Upserted the Entity" | order by timestamp desc ;

traces | where operation_Name == 'Receiver' | where message startswith "Processing Entity" | order by timestamp desc ;

traces | where operation_Name == 'Receiver' | where message startswith "Processing Entity" | where customDimensions.prop__actionCode == 'Add' | order by timestamp desc ;

traces | where operation_Name == 'Receiver'| where message startswith "Processed messageId: " | order by timestamp desc
| summarize count() by tostring(customDimensions.prop__rootName)

```

## KQL search tips

To search exceptions custom dimensions when the `EventId` is an object and we want to search for matching `Id` within this object.
``` Kusto
exceptions
| extend eventId = todynamic(tostring(customDimensions.EventId))
| extend id = eventId.Id
| where id == 3001 or id == 2001

OR

exceptions
| extend eventId = todynamic(tostring(customDimensions.EventId))
| extend id = eventId.Id
| where id == 3001 or id == 2001 or id == 1001
```

### KQL - How to search with message template

If logging is done in the following way via the code:
``` CSharp
log.LogInformation(
    "Completed to import {ItemCount} from files: {@JsonFileNames}, for archive: {FileName}",
     result.Count, fileNames, input.BlobName);
```

then you can search by using the message template in following way:
``` Kusto
traces
| where timestamp > ago(10d)
| where operation_Name  == 'NameOfTheOrchestrator'
| extend messageTemplate = customDimensions["prop__{OriginalFormat}"]
| where messageTemplate == 'Completed to import {ItemCount} from files: {@JsonFileNames}, for archive: {FileName}'
| order by timestamp asc
```

