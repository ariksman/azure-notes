

# Create Graph API client 

## Create Graph API client via secret or cert

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
