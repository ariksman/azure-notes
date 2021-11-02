# Bicep

- [Discussion available at public bicep github](https://github.com/Azure/bicep)
- [Most recent community calls available at youtube](https://www.youtube.com/channel/UCZZ3-oMrVI5ssheMzaWC4uQ/videos)


### Bicep resource functions

-[Resource functions for Bicep](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/bicep-functions-resource)

Above resource functions such as:
```
list
listKeys
listKeyValue

The actual rsult varies depending of the underlying resource. For example storage account the access is in format:
```
listKeys(resourceId, apiVersion).Keys[0].Value
