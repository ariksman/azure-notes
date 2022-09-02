## Create alerts via Bicep

It is possible to automate alert deployment with `bicep`. This leverages the `Microsoft.Insights/scheduledQueryRules@2018-04-16` and custom AI query.
To store info for the alerts `TelemetryClient` can be used in a following way:

``` CSharp
// Trigger alert by logging a custom event in AppInsights
_telemetryClient.TrackEvent(
eventName: "APP.Entities.NotReceived",
properties: new Dictionary<string, string>()
{
  { "InternalId", attachment.Id.ToString() },
  { "Name", entity.Name },
  { "Number", entity.Number },
  { "DocumentType", entity.Document.Type },
});
```

And the `TelemetryClient` can be registered for example to functions:
``` CSharp
builder.Services.AddSingleton(new TelemetryClient(telemetryConfiguration));
```


Automate deployment of this alert rule via `bicep`

``` bicep
resource api_entities_notReceived_alert 'Microsoft.Insights/scheduledQueryRules@2018-04-16' = {
  name: '${api.name}-entitiesNotReceived-${global.environment}'
  location: '${global.location}'
  properties: {
    displayName: '${api.name}-entitiesNotReceived-${global.environment}'
    description: 'It seems that not all entities have been delivered to the APP. \n\nPlease investigate what is the cause to this.'
    enabled: 'true'
    source: {
      query: 'customEvents\n| project timestamp, \n            name,\n            DocumentType = tostring(customDimensions["DocumentType"]),\n            InternalId = customDimensions["InternalId"],\n            Name = customDimensions["Name"],\n| where name == "APP.Entities.NotReceived"\n| order by timestamp desc, DocumentType desc'
      dataSourceId: resourceId('microsoft.insights/components', naming.appInsights)
      queryType: 'ResultCount'
    }
    schedule: {
      frequencyInMinutes: 5
      timeWindowInMinutes: 5
    }
    action: {
      'odata.type': 'Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction'
      severity: '2'
      aznsAction:{
        actionGroup: array(resourceId('microsoft.insights/actionGroups', naming.actionGroup))
        emailSubject: '[${toUpper(global.environment)}] APP - Entities are missing'
      }
      trigger: {
        threshold: 0
        thresholdOperator: 'GreaterThan'
      }
    }
  }
}
```
