Add a new Data lake ACL in following way

``` PowerShell
$fullAcl="other:objectId:rwx,default:other:objectId:rwx"
$newFullAcl = $fullAcl.Split(",")
Set-AzDataLakeStoreItemAclEntry -AccountName "DataLakeResourceName" -Path / -Acl $newFullAcl -Recurse -ShowProgress -Verbose
```

To remove Data Lake ACL
``` Powershell
$fullAcl="user:objectId:rwx,default:user:objectId:rwx"
$newFullAcl = $fullAcl.Split(",")
Remove-AzDataLakeStoreItemAclEntry -AccountName "DataLakeResourceName" -Path / -Acl $newFullAcl -Recurse -ShowProgress -Verbose
```
