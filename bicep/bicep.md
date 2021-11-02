# Bicep

- [Discussion available at public bicep github](https://github.com/Azure/bicep)
- [Most recent community calls available at youtube](https://www.youtube.com/channel/UCZZ3-oMrVI5ssheMzaWC4uQ/videos)


## Bicep resource functions

- [Resource functions for Bicep](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/bicep-functions-resource)

Above resource functions such as:
```
list
listKeys
listKeyValue
```
The actual result varies depending of the underlying resource. For example in-case of a storage account, the access is in following format.
```
listKeys(resourceId, apiVersion).keys[0].value
```
Note the response format `keys[0].value` used with the `listKeys` function is based on the resource REST API response:

<img src="azureStorageAccountListKeysResponse.png" width="600" />
