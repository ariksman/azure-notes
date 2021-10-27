# Application Insights

# Query traces = LogInformation

When logging information at the backend code with `logger.LogInformation` it will be available via Application Insights traces.
```
traces | where message startswith "Search string to look for.." | order by timestamp desc
```
