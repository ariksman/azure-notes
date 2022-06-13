Add a new Data lake ACL in following way

``` PowerShell
$fullAcl="other:objectId:rwx,default:other:objectId:rwx"
$newFullAcl = $fullAcl.Split(",")
Set-AzDataLakeStoreItemAclEntry -AccountName "DataLakeResourceName" -Path / -Acl $newFullAcl -Recurse -ShowProgress -Verbose
```
