
# CosmosDb SQL API

## How to query Azure cosmosDb with main document filtering

The source document is in following format:
``` Json
{
    "Name": "10451164",
    "Type": "Component",
    "Revision": "1",
    "Modified": "2021-10-17T07:10:24",
    "Originated": "2015-12-22T18:35:08",
    "Attributes": [
        {
            "Name": "Additional Sales Description",
            "Unit": null,
            "Value": "LONG TEXT VALUE - OVER 2000"
        },
        {
            "Name": "Sales Description Free Text",
            "Unit": null,
            "Value": "LONG TEXT VALUE - OVER 2000"
        },
        ...
```

Query syntax to filter based on main document attribute
``` sql
SELECT c.Name, c.Revision, c.Type, LENGTH(t['Value']) AS AttributeValueLenght
FROM c
JOIN t IN c.Attributes
WHERE LENGTH(t['Value']) > 2000
```
Result:
```
[
    {
        "Name": "10451164",
        "Revision": "1",
        "Type": "Component",
        "AttributeValueLenght": 2014
    },
    {
        "Name": "10451164",
        "Revision": "1",
        "Type": "Component",
        "AttributeValueLenght": 2014
    },
    ...
]
```
## How to query or filter based on array value:
``` sql
SELECT 
    c.Name,
    LENGTH(c['Value'])
FROM c IN c.Attributes
WHERE LENGTH(c['Value']) > 2000
```

## How to count all documents within container

Counting example with property filtering
``` sql
SELECT count(1) FROM c WHERE c.DocumentType = 'Item'
```
Result
```
[
    {
        "$1": 1234567890
    }
]
```

# Microsoft documentation - SQL API
[Getting started with SQL queries](https://learn.microsoft.com/en-us/azure/cosmos-db/sql/sql-query-getting-started)
