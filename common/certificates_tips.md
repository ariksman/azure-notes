
# How to get base64 encoded string from cert file

To quickly debug locally the certificate, the **user-secrets** can be leveraged with the base64 encoded string version of the cert-file.

## Method 1: Obtain cert string value

Following commands extract the `string` value and store it within a txt-file.
[get-content](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-content?view=powershell-7.2):

``` PowerShell
PS > $fileContentBytes = get-content '.\CN=OAuth2_CertFile_DEV.pfx' -Encoding Byte
PS > [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-encoded-bytes.txt'
```

This can be stored as a string into the projects user-secrets.

The same can be achieved with Python script:

```Python
import base64
import os
print(os.getcwd())

# Read the contents of the PFX file as bytes
with open('<name-of-the-cert.pfx>', 'rb') as f:
    file_content_bytes = f.read()

# Convert the bytes to a base64-encoded string
base64_encoded_bytes = base64.b64encode(file_content_bytes).decode()

# Write the base64-encoded string to a file
with open('pfx-encoded-bytes.txt', 'w') as f:
    f.write(base64_encoded_bytes)
```

