

# Create Graph API client 

## Create Graph API client via secret or cert

Example of `appsettings.json`
``` Json
"AzureAd": {
    "ClientId": "", // Application Id
    "TenantId": "", // Tenant / organization Id
    "ClientSecret": "", // If using passwrod
    "Certificate": "" // If using certificate
}

```

``` CSharp
internal static class AppSettings
{
    public static class AzureAd
    {
        public const string ClientId = "AzureAd:ClientId";
        public const string TenantId = "AzureAd:TenantId";
        public const string ClientSecret = "AzureAd:ClientSecret";
        public const string Certificate = "AzureAd:Certificate";
    }
}
```

Example code for graph client cration
``` CSharp
var clientId = configuration[AppSettings.AzureAd.ClientId];
var tenantId = configuration[AppSettings.AzureAd.TenantId];
var clientSecret = configuration[AppSettings.AzureAd.ClientSecre

var scopes = new[] { "https://graph.microsoft.com/.default"
var certName = configuration[AppSettings.AzureAd.Certificate];
var value = configuration[certName];

// If with Cert
var gwCertPrivateKeyBytes = Convert.FromBase64String(value);
var gwCert = new X509Certificate2(gwCertPrivateKeyBytes, "CertPasswordHere", X509KeyStorageFlags.MachineKeySe

// Create client app to fetch the token
var clientApp =
   ConfidentialClientApplicationBuilder
   .Create(clientId)
   .WithTenantId(tenantId)
   // .WithClientSecret(clientSecret)
   .WithCertificate(gwCert)
   .Build

// Fetch token from Graph API
var authResult = await clientApp
  .AcquireTokenOnBehalfOf(scopes, new UserAssertion(accessToken))
  .ExecuteAsync
  
// Create client
var graphClient = new GraphServiceClient(
    "https://graph.microsoft.com/v1.0",
    new DelegateAuthenticationProvider(async requestMessage =>
    {
        await Task.FromResult("");
        requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", authResult.AccessToken);
    }));
```

## Tenant wide admin consent

As tenant admin, grant global consent via this link
```
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

[Construct the URL for granting tenant-wide admin consent](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/grant-admin-consent#construct-the-url-for-granting-tenant-wide-admin-consent)
