### Use certificates automatic deployment to keyvault


Pipeline task to initiate the deployment. It is a good habit to provide the `keyvaultName` and `graphApiCertName` as ARM / Bicep output parameter. This allows you to deploy based on environments `dev/qa/prod`.

``` Yml
- task: AzurePowerShell@5
    displayName: "Upload the certificate to use Graph API"
    inputs:
        azureSubscription: "${{ parameters.azureSubscriptionName }}"
        azureSubscriptionId: "${{ parameters.azureSubscriptionId }}"
        ScriptType: FilePath
        ScriptPath: $(Pipeline.Workspace)/Deployment/Scripts/Upsert-KeyVaultCertificate.ps1
        # Just list secrets from variable groups to scriptArguments as shown
        ScriptArguments: >-
            -keyVaultName "$(keyVaultName)"
            -certificateName '$(graphApiCertName)'
            -certificatePath '$(Pipeline.Workspace)/Deployment/Certificates/${{ parameters.graphApiCertificateName }}'
            -certificatePassword '$(api-app-registration-client-certificate-password)'
        FailOnStandardError: true
        azurePowerShellVersion: "LatestVersion"
        pwsh: true
 ```
 
 The powershell upsert script used to upload certificate into keyvault.
```powershell
## Give in params as follows:
## Update-KeyVaultSecrets.ps1 -keyVaultName "mykeyvault" -certificateName "myCertificate" -certificatePath "pathToCertificate" -certificatePassword "password"
## This will result in a certificate named "myCertificate".

Param(
  [string] [Parameter(Mandatory=$true)] $keyVaultName,
  [string] [Parameter(Mandatory=$true)] $certificateName,
  [string] [Parameter(Mandatory=$true)] $certificatePath,
  [string] [Parameter(Mandatory=$true)] $certificatePassword
)

## Verify access to Key Vault
$keyVault = Get-AzKeyVault -Name $keyVaultName
if($null -eq $keyVault) {
  Write-Error "Key Vault could not be found"
}

## Check if a file can be found on the given certificateFilePath
$certificateFile = Get-Content $certificatePath -ErrorAction SilentlyContinue
if($null -eq $certificateFile) {
  Write-Error "No certificate could be found at the given location"
}

$securePassword = ConvertTo-SecureString $certificatePassword -AsPlainText -Force
Import-AzKeyVaultCertificate -VaultName $keyVaultName -Name $certificateName -FilePath $certificatePath -Password $securePassword
```
                                
