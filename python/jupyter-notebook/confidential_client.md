# Obtain token for Azure resources

It is possible to fetch a token from App Registration and use this token to access other resources:

```Python
import msal

# Replace these placeholders with your own values
TENANT_ID = '<tenant_id>'
CLIENT_ID = '<client_id>'
CLIENT_SECRET = '<client_secret>'

# Authenticate the user and obtain an access token
app = msal.ConfidentialClientApplication(CLIENT_ID, authority=f"https://login.microsoftonline.com/{TENANT_ID}", client_credential=CLIENT_SECRET)

result = None
scopes = ["https://database.windows.net/.default"]

result = app.acquire_token_for_client(scopes)

access_token = result["access_token"]
```
