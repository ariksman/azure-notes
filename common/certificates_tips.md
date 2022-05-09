
# How to get base64 encoded string from cert file

To quickly debug locally the certificate, the user-secrets can be leveraged with the base64 encoded string version of the cert-file.

## Method 1: Obtain cert string value

Following commands extract the `string` value and store it within a txt-file.
[Aget-content command](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-content?view=powershell-7.2):

``` PowerShell
> $fileContentBytes = get-content '.\CN=OAuth2_CertFile_DEV.pfx' -Encoding Byte
> [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-encoded-bytes.txt'
```
