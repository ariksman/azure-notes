
Add the WebApp or FunctionApp correct rights to access the Storage account Blob

``` xml
"variables": {
  "containerName": "itemsexported",
  "storageBlobContributorRoleId": "[concat(subscription().Id, '/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe')]"
},
```

Example storage account
``` xml
{
  "name": "[parameters('storageAccount')]",
  "type": "Microsoft.Storage/storageAccounts",
  "location": "[resourceGroup().location]",
  "apiVersion": "2021-06-01",
  "dependsOn": [],
  "tags": {
    "displayName": "Storage Account"
  },
  "sku": {
    "name": "[parameters('storageAccountType')]"
  }
},
```

Define access rights for `functionApp` to access `containerName` within storage account
``` xml
{
  "type": "Microsoft.Storage/storageAccounts/blobServices/containers/providers/roleAssignments",
  "apiVersion": "2021-06-01",
  "name": "[concat(parameters('storageAccount'), '/default/', variables('containerName'), '/Microsoft.Authorization/', guid(resourceGroup().id, 'funcAppFilesAccess'))]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccount'))]",
    "[resourceId('Microsoft.Storage/storageAccounts/blobServices/containers', parameters('storageAccount'), 'default', variables('containerName'))]",
    "[resourceId('Microsoft.Web/sites', parameters('functionAppName'))]"
  ],
  "properties": {
    "principalId": "[reference(resourceId('Microsoft.Web/sites', parameters('functionAppName')), '2016-08-01', 'Full').identity.principalId]",
    "roleDefinitionId": "[variables('storageBlobContributorRoleId')]"
  }
},
},
```
