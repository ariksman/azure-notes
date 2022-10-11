# Graph explorer

[Link to graph-explorer](https://developer.microsoft.com/en-us/graph/graph-explorer)

Sometimes it is difficult to find what is behinf pipeline errors `object-id`, for example what is the display name of a service connections. 
Therefore it was handy to utilize the graph-explorer for this task. 

First I wanted to find out what is the individual service principal name based on object id:
```
https://graph.microsoft.com/v1.0/servicePrincipals/{object-id}
```

It is also possible to search suitable service principals based on their display name: `string:test-filtering-name`
```
https://graph.microsoft.com/v1.0/servicePrincipals?$count=true&$search="displayName:test-filtering-name"&$select=id,displayName
```

Example result of the query

``` json
{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#servicePrincipals(id,displayName)",
    "@odata.count": 3,
    "value": [
        {
            "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
            "displayName": "test-filtering-name-we-prod-rg"
        },
        {
            "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
            "displayName": "test-filtering-name-we-qa-rg"
        },
        {
            "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
            "displayName": "test-filtering-name-we-dev-rg"
        }
    ]
}
```
