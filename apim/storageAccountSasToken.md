It is possible to directly create a SAS token for storage account file via api-management policys

The `storage-accountkey` and `storage-accountname` can be stored within the apim `named values` as secrets and accessed from the policy.
``` XML
<!-- extract parameters from URL or use static values -->
<set-variable name="containerName" value="{ContainerNameWithinStorageAccount}" />
<set-variable name="blobName" value="{BLOB-name}" />
<!-- create SAS-token to access data on blob storage -->
<!-- Initialize context variables with property values. -->
<set-variable name="accessKey" value="{{storage-accesskey}}" />
<set-variable name="expiryInSeconds" value="3600" />
<set-variable name="storageAccount" value="{{storage-accountname}}" />
<set-variable name="x-ms-date" value="@(DateTime.UtcNow)" />
<set-variable name="signedPermissions" value="r" />
<set-variable name="signedService" value="b" />
<set-variable name="signedStart" value="@(((DateTime)context.Variables["x-ms-date"]).ToString("yyyy-MM-ddTHH:mm:ssZ"))" />
<set-variable name="signedExpiry" value="@(((DateTime)context.Variables["x-ms-date"]).AddSeconds(Convert.ToInt32((string)context.Variables["expiryInSeconds"])).ToString("yyyy-MM-ddTHH:mm:ssZ"))" />
<set-variable name="signedProtocol" value="https" />
<set-variable name="signedVersion" value="2017-07-29" />
<set-variable name="canonicalizedResource" value="@{
    return string.Format("/blob/{0}/{1}/{2}",
        (string)context.Variables["storageAccount"],
        (string)context.Variables["containerName"],
        (string)context.Variables["blobName"]
    );
}" />
<set-variable name="signedIP" value="" />
<set-variable name="signedIdentifier" value="" />
<set-variable name="rscc" value="" />
<set-variable name="rscd" value="" />
<set-variable name="rsce" value="" />
<set-variable name="rscl" value="" />
<set-variable name="rsct" value="" />
```

``` XML
<!-- Build the string to form signature. -->
<set-variable name="StringToSign" value="@{
    return string.Format("{0}\n{1}\n{2}\n{3}\n{4}\n{5}\n{6}\n{7}\n{8}\n{9}\n{10}\n{11}\n{12}",
        (string)context.Variables["signedPermissions"],
        (string)context.Variables["signedStart"],
        (string)context.Variables["signedExpiry"],
        (string)context.Variables["canonicalizedResource"],
        (string)context.Variables["signedIdentifier"],
        (string)context.Variables["signedIP"],
        (string)context.Variables["signedProtocol"],
        (string)context.Variables["signedVersion"],
        (string)context.Variables["rscc"],
        (string)context.Variables["rscd"],
        (string)context.Variables["rsce"],
        (string)context.Variables["rscl"],
        (string)context.Variables["rsct"]
    );
}" />
```

``` XML
<!-- Build/Hash the signature. -->
<set-variable name="Signature" value="@{
    byte[] SignatureBytes = System.Text.Encoding.UTF8.GetBytes((string)context.Variables["StringToSign"]);
    System.Security.Cryptography.HMACSHA256 hasher = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String((string)context.Variables["accessKey"]));
    return Convert.ToBase64String(hasher.ComputeHash(SignatureBytes));
}" />
```

``` XML
<!-- Form the sasToken. -->
<set-variable name="SasToken" value="@{
    return string.Format("sv={0}&sr={1}&sp={2}&st={3}&se={4}&spr={5}&sig={6}",
        (string)context.Variables["signedVersion"],
        (string)context.Variables["signedService"],
        (string)context.Variables["signedPermissions"],
        System.Net.WebUtility.HtmlEncode((string)context.Variables["signedStart"]),
        System.Net.WebUtility.HtmlEncode((string)context.Variables["signedExpiry"]),
        (string)context.Variables["signedProtocol"],
        System.Net.WebUtility.HtmlEncode((string)context.Variables["Signature"])
    );
}" />
```

``` XML
<!-- Form the complete Url. -->
<set-variable name="FullUrl" value="@{
    return string.Format("https://{0}.blob.core.windows.net/{1}/{2}?{3}",
        (string)context.Variables["storageAccount"],
        (string)context.Variables["containerName"],
        (string)context.Variables["blobName"],
        (string)context.Variables["SasToken"]
    ).Replace("+", "%2B");
}" />
<set-variable name="metadataUrl" value="@{
    return string.Format("https://{0}.blob.core.windows.net/{1}/{2}?comp=metadata&{3}",
        (string)context.Variables["storageAccount"],
        (string)context.Variables["containerName"],
        (string)context.Variables["blobName"],
        (string)context.Variables["SasToken"]
    ).Replace("+", "%2B");
}" />
```

Additionally, it makes sense that APIM contains the logic to verify that the blob actually exist. If it does not we should return `404`
``` XML
<!-- Check existance of the blob before returning 200 OK with sas token. -->
<send-request mode="new" response-variable-name="blobMetadata" timeout="30" ignore-error="false">
    <set-url>@((string)context.Variables["metadataUrl"])</set-url>
    <set-method>GET</set-method>
</send-request>
<choose>
    <!-- Check active property in response -->
    <when condition="@(((IResponse)context.Variables["blobMetadata"]).StatusCode == 404)">
        <!-- Return 404 Not Found with http-problem payload -->
        <return-response>
            <set-status code="404" reason="Not Found" />
            <set-header name="Content-Type" exists-action="override">
                <value>application/json</value>
            </set-header>
            <set-body>@{
                return new JObject(
                        new JProperty("error", "not_found"),
                        new JProperty("error_description", "No data could be found for the given parameters."),
                        new JProperty("timestamp", DateTime.UtcNow.ToString("yyyy-MM-dd HH:mm:ssZ"))
                    ).ToString();
            }</set-body>
        </return-response>
    </when>
    <otherwise>
        <!-- return response -->
        <return-response>
            <set-status code="200" reason="OK" />
            <set-header name="Content-Type" exists-action="override">
                <value>application/json</value>
            </set-header>
            <set-body>@{
                var fullUrl = ((string)context.Variables["FullUrl"]);
                var remainingValidDuration = ((Convert.ToDateTime((string)context.Variables["signedExpiry"])) - DateTime.Now).TotalSeconds;
                
                return new JObject(
                        new JProperty("url", fullUrl),
                        new JProperty("expiresIn", Math.Floor(remainingValidDuration).ToString()),
                        new JProperty("timestamp", DateTime.UtcNow.ToString("yyyy-MM-dd HH:mm:ssZ"))
                    ).ToString();
            }</set-body>
        </return-response>
    </otherwise>
</choose>
```
