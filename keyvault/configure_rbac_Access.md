

## Commands to list and delete assigned RBAC roles from resource group level

Via bicep RBAC is possible to assing with the following module:
```bicep
param keyVaultName string
param identityPrincipalId string

resource keyVault 'Microsoft.KeyVault/vaults@2022-07-01' existing = {
    name: keyVaultName
}

resource apiSecretsUser 'Microsoft.Authorization/roleAssignments@2020-08-01-preview' = {
    name: guid('${keyVault.id}-${identityPrincipalId}-KeyVaultSecretsUser')
    scope: keyVault
    properties: {
        // Key Vault Secrets User
        roleDefinitionId: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '4633458b-17de-408a-b874-0445c86b69e6')
        principalId: identityPrincipalId
    }
}
```

The following az cli command allow deletion and listing of currently active roles for resource group

```
az role assignment list --scope '/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupName}' --role 'Key Vault Secrets User'
```

Delete rbac roles based on the resource ids:

```
az role assignment delete --ids "{Id}"
```
, where the id is the above list command `id` property, example format: `"/subscriptions/{SubscriptionId}/resourcegroups/{ResourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{Guid}"`
