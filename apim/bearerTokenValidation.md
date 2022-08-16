API management allows adding `bearer` token validation as a policy. The documentation covers many use-cases: https://docs.microsoft.com/en-us/azure/api-management/api-management-access-restriction-policies#ValidateJWT

``` XML
<!-- validate the JWT-token and verify whether the the required role has been assigned. -->
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized" require-expiration-time="true" require-scheme="Bearer" require-signed-tokens="true">
    <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
    <audiences>
        <audience>{audience string}</audience>
    </audiences>
    <issuers>
        <issuer>{issuer string}</issuer>
    </issuers>
</validate-jwt>
```

As default the `<openid-config />` adds issuer validation from `https://login.windows.net/xxx` but one can add more valid issuers via `<issuers />` property.
