## Service principal

A Service Principal is an AAD Application's representation in a tenant, or "an identity for the app". It can be added like a user in Azure's


## Service Principal for yaml-pipeline

Create a new `ServicePrincipal` via tenants Azure AD portal. Add new **App Registration**. 
When manually connecting devops pipelines to the created service principal, following information is required:
- Subscription Id
- Subscription name
- Tenant Id
- Service principal Id
- Service principal key

The key can be created via service principals **Certificates & Secrets** menu as a normal client secret.

Connection between devops and azure tenant can be now added to the devops pipelines. 




